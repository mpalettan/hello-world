# hello-world
My first repository

My name is Mauricio Paletta. 

I am a Computer Science Engineer and I hold a master in Artificial Intelligence and a Ph.D. in Computer Science.

I am interesting in the Data Science area. I am currently taking a course in Coursera.org.



INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 8551, '4', 855, 3, 5, 3)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 8551, '3', 855, 3, 5, 2)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 8551, '2', 855, 3, 5, 1)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 8551, '1', 855, 3, 5, 0)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 9999, '1', 3838, 0, NULL, 5)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 9999, '2', 3837, 0, NULL, 6)
INSERT INTO [dbo].[ReceptoraCriterioAlarmaXMensajeDesconocido] ([IdTipoVariable],[Valor],[IdTipoActivoXIndicador],[IdCategoriaActivo],[IdActivo],[ResultadoMensaje]) VALUES ( 8561, '1', 856, 0, NULL, 4)

INSERT INTO [dbo].[ReglaEvento] ( IdReglaEvento, Descripcion, IdTipoIntervalo, FueraDelIntervalo, IdIndicador, IdEstado, IdAplicacion, CierreManual, Sonido, Habilitado, Prioridad, IdColorFondo, IdColorFuente, IdTipoActivo, IdCategoriaActivo, 
                         IdPeriodicidadIndicador, IdTipoDatoAdicional, ValorDatoAdicional, IdPlanActuacion, PorRequerimiento, IdEncuesta, IdGrupoEstado, IdTipoRelacion, IdMensaje, MinEsperaMensaje
) VALUES (3001,	'DEVICE NOT REGISTERED',	NULL,	False,	'UNKNOWN',	2,	6,	True,	NULL,	True,	10,	4227327,	0,	NULL,	NULL,	'UNKNOWN_DEVICE',	NULL,	NULL,	NULL,	False,	NULL,	NULL,	NULL,	NULL,	NULL)
INSERT INTO [dbo].[ReglaEvento] ( IdReglaEvento, Descripcion, IdTipoIntervalo, FueraDelIntervalo, IdIndicador, IdEstado, IdAplicacion, CierreManual, Sonido, Habilitado, Prioridad, IdColorFondo, IdColorFuente, IdTipoActivo, IdCategoriaActivo, 
                         IdPeriodicidadIndicador, IdTipoDatoAdicional, ValorDatoAdicional, IdPlanActuacion, PorRequerimiento, IdEncuesta, IdGrupoEstado, IdTipoRelacion, IdMensaje, MinEsperaMensaje
) VALUES(3000,	'EQUIPMENT NOT REGISTERED',	NULL,	False,	'UNKNOWN_MESSAGE',	2,	6,	True,	NULL,	True,	10,	4210931,	14536083,	NULL,	0,	'UNKNOWN_MESSAGE',	NULL,	NULL	,NULL,	False,	NULL,	NULL,	NULL,	NULL,	NULL)
INSERT INTO [dbo].[ReglaEvento] ( IdReglaEvento, Descripcion, IdTipoIntervalo, FueraDelIntervalo, IdIndicador, IdEstado, IdAplicacion, CierreManual, Sonido, Habilitado, Prioridad, IdColorFondo, IdColorFuente, IdTipoActivo, IdCategoriaActivo, 
                         IdPeriodicidadIndicador, IdTipoDatoAdicional, ValorDatoAdicional, IdPlanActuacion, PorRequerimiento, IdEncuesta, IdGrupoEstado, IdTipoRelacion, IdMensaje, MinEsperaMensaje
) VALUES(3002,	'CONTACT ID ISSUE',	NULL,	False,	'UNKNOWN_MESSAGE',	1,	6,	True,	NULL,	True,	10,	65535,	0,	NULL,	NULL,	'UNKNOWN_MESSAGE',	NULL,	NULL,	NULL,	False,	NULL,	NULL,	NULL,	NULL,	NULL)



GO
ALTER FUNCTION  [dbo].[VariableAplicaMensajeReceptora]
(
	@IdTipoActivoXIndicador int,@IdTipoVariable bigint, @IdConsecutivo smallint, @Mensaje varchar(100) 
)
RETURNS   @Retorno TABLE (Resultado int, DescripcionResultado varchar(500),IdTipoVariable bigint, IdTipoActivoXIndicador int, IdActivo bigint, IdCategoriaActivo smallint, IdConsecutivo smallint, ValorAsignado varchar(50), IdEquipo bigint, IdParticion varchar(5), IdUsuario varchar(5))
AS
BEGIN

	declare @IdTipoMensaje char(1);
	declare @IdAlternativoPadre varchar(100);
	declare @IdAlternativoDispositivo varchar(100);
	declare @Descripcion varchar(50);
	declare @IdTipoActivo int;	
	declare @IdEquipo bigint;
	declare @IdAlternativoPadreBuscado varchar(100);
	declare @IdAlternativoHijoBuscado varchar(100);
	declare @IdAlternativoHijoAux varchar (100);
	declare @Campos TABLE (IdTipoMensaje char(1),IdCampo varchar(50), CaracterInicial smallint, CaracterFinal smallint, IdTipoDatoVariable int, Valor varchar(50),ValorOriginal varchar(50), Descripcion varchar(100));
	declare @ValorRetorno varchar(50);
	declare @IdCampoRetorno varchar(50);
	declare @CantidadDeCamposVariable int;
	
	
	declare @IdParticionBuscado varchar(5);
	declare @IdUsuarioBuscado varchar(5);

	--SET @IdTipoMensaje = substring(@Mensaje, 1,1);
	SET @IdTipoMensaje = '0';
	if NOT exists (SELECT IdTipoMensaje FROM ReceptoraTipoMensaje WHERE IdTipoMensaje = @IdTipoMensaje)
	begin
		INSERT INTO @Retorno (Resultado,DescripcionResultado) values (0, 'CONFIGURATION PROBLEM: MESSAGE TYPE DOES NOT EXISTS');
	end
	else
	begin
		INSERT INTO @Campos SELECT * FROM dbo.ObtenerMensajeReceptora(@Mensaje);
		
		if (NOT exists (SELECT * FROM @Campos))
		begin
			INSERT INTO @Retorno (Resultado,DescripcionResultado) values (2,'CONFIGURATION PROBLEM: THERE ARE NO FIELDS ASSOCIATED WITH THE CURRENT MESSAGE');
		end
		else
		begin
			SET @CantidadDeCamposVariable = (SELECT count(*) FROM ReceptoraCampoXTipoVariable with(nolock) WHERE  IdConsecutivo = @IdConsecutivo and IdTipoVariable = @IdTipoVariable and IdTipoMensaje = @IdTipoMensaje and IdTipoActivoXIndicador = @IdTipoActivoXIndicador)
			
			if (@CantidadDeCamposVariable = 0)
			begin
				/*aqui hace falta evaluar si existen campos en la variable prueba SELECT * FROM ProcesarMensajeD6600('x012 408B18140101001') */
				INSERT INTO @Retorno (Resultado,DescripcionResultado) values (3,'CONFIGURATION PROBLEM: THERE ARE NO FIELDS ASSOCIATED WITH ONE OF THE VARIABLES');
			end
			else if ((SELECT COUNT(*) FROM   ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
							  @Campos AS O ON V.IdTipoMensaje = O.IdTipoMensaje AND V.IdCampo = O.IdCampo AND O.Valor like replace(V.Valor,'#','%')
				WHERE V.IdTipoVariable = @IdTipoVariable and V.IdConsecutivo = @IdConsecutivo and V.Valor NOT IN ('$','[E]','[A]','[P]', '[U]' )) =
				(SELECT count(distinct IdCampo) FROM ReceptoraCampoXTipoVariable with(nolock) WHERE  IdTipoVariable = @IdTipoVariable and IdConsecutivo = @IdConsecutivo and IdTipoMensaje = @IdTipoMensaje AND Valor NOT IN ('$','[E]','[A]','[P]', '[U]' )))
				begin
					SELECT @IdTipoActivo = IdTipoActivo FROM TipoActivoXIndicador WHERE IdCategoriaActivo = 0 and IdTipoActivoXIndicador = @IdTipoActivoXIndicador
				
					SELECT @ValorRetorno = ValorRetorno, @IdCampoRetorno = IdCampoRetorno FROM ReceptoraTipoMensajeXTipoVariable with(nolock) WHERE IdTipoVariable = @IdTipoVariable and IdTipoMensaje = @IdTipoMensaje and IdConsecutivo = @IdConsecutivo 
					if (@ValorRetorno is null)
						SELECT @ValorRetorno = dbo.ConvertirTipoDato(Valor, IdTipoDatoVariable) FROM @Campos WHERE IdCampo = @IdCampoRetorno

					
					SET @IdAlternativoPadreBuscado = (SELECT TOP 1 O.Valor FROM ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
										@Campos AS O ON V.IdCampo = O.IdCampo AND V.IdTipoMensaje = O.IdTipoMensaje WHERE V.Valor = '[A]' AND  V.IdTipoVariable =@IdTipoVariable and V.IdConsecutivo = @IdConsecutivo)
					
					

					SET @IdAlternativoHijoBuscado = (SELECT TOP 1 O.Valor FROM ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
										@Campos AS O ON V.IdCampo = O.IdCampo AND V.IdTipoMensaje = O.IdTipoMensaje WHERE V.Valor = '$'  AND ISNUMERIC(O.Valor) = 1 AND V.IdTipoVariable =@IdTipoVariable and V.IdConsecutivo = @IdConsecutivo)
					SET @IdAlternativoHijoAux = (SELECT TOP 1 O.ValorOriginal FROM ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
										@Campos AS O ON V.IdCampo = O.IdCampo AND V.IdTipoMensaje = O.IdTipoMensaje WHERE V.Valor = '$'  AND ISNUMERIC(O.Valor) = 1 AND V.IdTipoVariable =@IdTipoVariable and V.IdConsecutivo = @IdConsecutivo)

					set @IdParticionBuscado = (SELECT TOP 1 O.Valor FROM ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
										@Campos AS O ON V.IdCampo = O.IdCampo AND V.IdTipoMensaje = O.IdTipoMensaje WHERE V.Valor = '[P]'  AND  V.IdTipoVariable =@IdTipoVariable and V.IdConsecutivo = @IdConsecutivo)
					set @IdUsuarioBuscado = (SELECT TOP 1 O.Valor FROM ReceptoraCampoXTipoVariable AS V with(nolock) INNER JOIN
									@Campos AS O ON V.IdCampo = O.IdCampo AND V.IdTipoMensaje = O.IdTipoMensaje WHERE V.Valor = '[U]'  AND  V.IdTipoVariable =@IdTipoVariable and V.IdConsecutivo = @IdConsecutivo)
					
					
					--AGREGAR EL CASO PARA BUSCAR EL PADRE DEL PADRE PARA VERIFICAR SI EL PADRE ES NULL EN ESTE CASO RETORNAR LA ALARMA DE ERROR DE CONFIGURACION -- EXISTE TODO PERO NO ESTA CONFIGURADA UNA UBICACION
					--AGREGAR EL CASO PARA 
					if (@IdAlternativoPadreBuscado is null)
						SET @Descripcion = 'ALL'
					if (@IdAlternativoHijoBuscado is null and @IdAlternativoHijoAux is null)
					begin

						SELECT top 1 @Descripcion = Descripcion, @IdEquipo = IdEquipo FROM Equipo WHERE IdAlternativo = @IdAlternativoPadreBuscado
						
					end
					else  
					begin
						
						
						SELECT top 1 @Descripcion = E.Descripcion + '-' + D.Descripcion, @IdEquipo = D.IdEquipo
						FROM Equipo AS D INNER JOIN Equipo AS E ON D.IdActivoPadre = E.IdEquipo  and D.IdCategoriaActivoPadre = 0 WHERE E.IdAlternativo = @IdAlternativoPadreBuscado and (D.IdAlternativo = @IdAlternativoHijoBuscado or D.IdAlternativo = @IdAlternativoHijoAux) and D.IdTipoEquipo = @IdTipoActivo;
					end
					
					if (@Descripcion is NOT null /*and  NOT exists(SELECT   * FROM     ReceptoraCampoXTipoVariable AS V with(nolock)  WHERE V.Valor = '$' AND  V.IdTipoVariable = @IdTipoVariable and V.IdConsecutivo = @IdConsecutivo AND V.IdTipoMensaje =@IdTipoMensaje)*/)
					begin
						INSERT INTO @Retorno SELECT     9 , 
										'DEVICE:' + @Descripcion, 
										@IdTipoVariable,
										IdTipoActivoXIndicador,
										IdActivo,
										IdCategoriaActivo, 
										@IdConsecutivo, 
										@ValorRetorno,
										@IdEquipo,
										@IdParticionBuscado,
										@IdUsuarioBuscado
											FROM         VW_Variable WITH (nolock) 
											WHERE     (IdTipoVariable = @IdTipoVariable) AND (IdCategoriaActivo = 0) AND (IdActivo = ISNULL(@IdEquipo, IdActivo))
				
					
					end
					if (NOT exists (SELECT * FROM @Retorno))
					begin		
						if (@Descripcion is NOT null)
						begin
								-- PROBLEMA CONFIGURACION DE VARIABLE
								INSERT INTO @Retorno SELECT 4, 'THE EQUIPMENT EXISTS BUT THE CURRENT DEVICE TYPE DOES NOT MATCH. PLEASE VERIFY THAT THE DEVICE TYPE EXPECTED IS THE SAME AS THE DEVICE TYPE ON "TYPE OF ASSET PER POINT" WITH THE ID NUMBER: ' + Convert(varchar,@IdTipoActivoXIndicador),
												--'Existe el Equipo '+@Descripcion+' IdEquipo:'+convert(varchar,@IdEquipo)+' pero no existe variable asociada del tipo:'+Convert(varchar,@IdTipoVariable)+' TipoActivoXIndicador:'+Convert(varchar,@IdTipoActivoXIndicador), 
												@IdTipoVariable,null,null,null, @IdConsecutivo,@ValorRetorno, @IdEquipo,null, null
						
						end
						else
						begin
							SELECT @Descripcion = Descripcion, @IdEquipo = IdEquipo FROM Equipo WHERE IdAlternativo = @IdAlternativoPadreBuscado
							if (@IdEquipo is null)
							begin
								--CHANGE SET Resultado ERA 3 AHORA CAMBIARA A 5 SERA DIFERENTE PARA QUE EL IDACTIVO ESTE INVALIDADO Y SE ASOCIE A LAS RECEPTORAS Y ASI PODER ACTUALIZAR SUS HIJOS 
								INSERT INTO @Retorno SELECT 6,
												'THERE IS NOT EQUIPMENT REGISTERED FOR THIS MESSAGE. VERIFY THAT THE EQUIPMENT TYPE EXPECTED IS THE SAME AS THE EQUIPMENT TYPE ON "ASSET TYPE PER POINT" WITH THE ID NUMBER: ' +Convert(varchar,@IdTipoActivoXIndicador),
												--'Existe tipovariable asociado pero no existe equipo asociado al mensaje. TipoActivoXIndicador:'+Convert(varchar,@IdTipoActivoXIndicador), 
												@IdTipoVariable,null,null,null, @IdConsecutivo,@ValorRetorno, null,null, null
							end
							else
							--MAL CONTACT ID
								
								DECLARE @IdActivo Bigint;
								DECLARE @IdHijoBuscado Bigint;
								SELECT TOP (1) @IdHijoBuscado = IdEquipo FROM Equipo where IdActivoPadre = @IdEquipo AND (IdAlternativo = @IdAlternativoHijoBuscado OR IdAlternativo = @IdAlternativoHijoAux);
								SET @IdActivo= isnull(@IdHijoBuscado, @IdEquipo)
								IF @IdHijoBuscado is not null
								BEGIN
									INSERT INTO @Retorno SELECT 5,
												'THERE IS A PROBLEM WITH THE EQUIPMENT NUMBER: '+Convert(varchar,@IdEquipo)+' WITH NAME: '+@Descripcion +'. WE CANNOT MATCH THE CONTACT ID OF THE DEVICE WITH ALTERNATIVE ID: ' + isnull(@IdAlternativoHijoBuscado,'') +'. PLEASE CHECK AND MODIFY THE DEVICE THAT MATCH WITH THE TYPE ID: '+Convert(varchar,@IdTipoActivo)+' AND THE "ASSET TYPE PER POINT" WITH ID NUMBER: '+Convert(varchar,@IdTipoActivoXIndicador), 
												--@IdTipoVariable,null,null,null, @IdConsecutivo,@ValorRetorno, @IdEquipo, @IdParticionBuscado, @IdUsuarioBuscado
												@IdTipoVariable,null,@IdActivo,0, @IdConsecutivo,@ValorRetorno, @IdEquipo, null, null
								END
								ELSE
								BEGIN
									INSERT INTO @Retorno SELECT 4,
												'THERE IS A PROBLEM WITH THE EQUIPMENT NUMBER: '+Convert(varchar,@IdEquipo)+' WITH NAME: '+@Descripcion +'. WE DID NOT FIND ANY DEVICE WITH ALTERNATIVE ID NUMBER: ' + isnull(@IdAlternativoHijoBuscado,'') +'. PLEASE CREATE A DEVICE WITH THE TYPE ID: '+Convert(varchar,@IdTipoActivo)+' AND THE "ASSET TYPE PER POINT" WITH ID NUMBER: '+Convert(varchar,@IdTipoActivoXIndicador), 
												--@IdTipoVariable,null,null,null, @IdConsecutivo,@ValorRetorno, @IdEquipo, @IdParticionBuscado, @IdUsuarioBuscado
												null,null,@IdActivo,0, @IdConsecutivo,@ValorRetorno, @IdEquipo, null, null
								END
								
						end
					end	
			END
			else
			begin
				INSERT INTO @Retorno SELECT 2,'CONFIGURATION PROBLEM. THE VARIABLE DOES NOT MATCH WITH THE EXISTING FIELDS', null,null,null,null,null,@ValorRetorno, null, null, null
			end
		END
	end
	return;
END



GO
ALTER FUNCTION  [dbo].[ProcesarMensajeReceptora]
(
	-- Add the parameters for the function here
	@Mensaje varchar(100)
)
--CHANGE SET SE MODIFICA RETORNO PARA RETORNAR EL IDEQUIPO
RETURNS   @Retorno TABLE (IdTipoMensaje	char(1),IdTipoVariable bigint,IdConsecutivo	smallint,Resultado	smallint,Mensaje	varchar(100),Valor	varchar(50),IdTipoActivoXIndicador int, IdActivo bigint, IdCategoriaActivo smallint,DescripcionResultado	varchar(500), IdEquipo bigint, IdParticion varchar(5), IdUsuario varchar(5))
AS
BEGIN
	declare @IdTipoMensaje char(1);
	declare @Cant smallint;
	declare @IdTipoActivoXIndicador int;
	declare @IdTipoVariable bigint;
	declare @IdConsecutivo smallint;
	
	--SET @IdTipoMensaje = substring(@Mensaje,1,1);
	SET @IdTipoMensaje = '0';
	
	DECLARE VariableCursor cursor for
	SELECT  IdTipoVariable, IdConsecutivo, IdTipoActivoXIndicador FROM ReceptoraTipoMensajexTipoVariable WHERE IdTipoMensaje = @IdTipoMensaje
	open VariableCursor;
	
	FETCH NEXT FROM VariableCursor INTO @IdTipoVariable, @IdConsecutivo,@IdTipoActivoXIndicador;
	while @@FETCH_STATUS = 0
	begin
		INSERT INTO @Retorno ( IdTipoMensaje, IdTipoVariable, IdConsecutivo, Resultado, Mensaje, Valor,			 IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado, IdEquipo, IdParticion, IdUsuario)
		SELECT			      @IdTipoMensaje,@IdTipoVariable, @IdConsecutivo,Resultado, @Mensaje, ValorAsignado, IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado, IdEquipo, IdParticion, IdUsuario FROM
		dbo.VariableAplicaMensajeReceptora(@IdTipoActivoXIndicador, @IdTipoVariable, @IdConsecutivo, @Mensaje);
		FETCH NEXT FROM VariableCursor INTO @IdTipoVariable, @IdConsecutivo,@IdTipoActivoXIndicador;

	end
	close VariableCursor;
	deallocate VariableCursor;
	return;
END



ALTER procedure [dbo].[ReceptoraProcesarMensaje] @Mensaje varchar(100), @FechaHora datetime
as
begin
	
DECLARE @Aux TABLE (IdTipoMensaje	char(1),IdTipoVariable	bigint,IdConsecutivo	smallint,Resultado	smallint,Mensaje	varchar(100),
Valor	varchar(50),IdTipoActivoXIndicador int, IdActivo bigint, IdCategoriaActivo smallint, 
DescripcionResultado	varok char(500), IdEquipo bigint, IdParticion varchar(5), IdUsuario varchar(5))

	IF (NOT exists (SELECT top 1 * FROM ReceptoraMensajeExcluido WHERE Mensaje = @Mensaje) )
	BEGIN
		INSERT INTO @Aux SELECT * FROM ProcesarMensajeReceptora(@Mensaje) 
		IF (exists (SELECT top 1 Resultado FROM @Aux WHERE Resultado = 9))
		BEGIN
			DELETE FROM @Aux WHERE Resultado != 9
			UPDATE @Aux SET Resultado = 4 WHERE Resultado = 9
			INSERT INTO ReceptoraMensaje SELECT @FechaHora, @Mensaje, 1, 0;
		END
		ELSE
		BEGIN
				DECLARE @Resultado smallint;
				DECLARE @IdTipoVariableResultado int;
				DECLARE @ValorResultado varchar (255);
				DECLARE @TipoActivoXIndicadorResultado int;
				DECLARE @IdCategoriaActivoResultado int ;
				DECLARE @IdActivoResultado int;
				
				SELECT @Resultado = ResultadoMensaje, @IdTipoVariableResultado = IdTipoVariable, @ValorResultado = Valor, @TipoActivoXIndicadorResultado = IdTipoActivoXIndicador, @IdCategoriaActivoResultado = IdCategoriaActivo, @IdActivoResultado = IdActivo 
				FROM ReceptoraCriterioAlarmaXMensajeDesconocido WHERE ResultadoMensaje = (SELECT TOP (1) Resultado FROM @Aux ORDER BY Resultado DESC);
				
				IF @Resultado = 5
					BEGIN
						
						DECLARE @IdTipoActivoXIndicador Int;
						DECLARE @IdTipoVariable Int;
						DECLARE VariableCursor CURSOR FOR SELECT IdTipoActivoXIndicador, IdTipoVariable FROM VariableValorActualIndicador where IdActivo = (SELECT TOP (1) IdActivo FROM @Aux WHERE Resultado = @Resultado)
							OPEN VariableCursor
								FETCH NEXT FROM VariableCursor INTO @IdTipoActivoXIndicador, @IdTipoVariable;
								WHILE @@FETCH_STATUS = 0
								BEGIN
									INSERT INTO @Aux SELECT TOP (1) * FROM @Aux WHERE Resultado = @Resultado;
									UPDATE TOP (1) @Aux SET  Resultado = 4, IdTipoActivoXIndicador = @IdTipoActivoXIndicador, IdTipoVariable = @IdTipoVariable WHERE Resultado = @Resultado;
									FETCH NEXT FROM VariableCursor INTO @IdTipoActivoXIndicador, @IdTipoVariable;
								END
							CLOSE VariableCursor
							DEALLOCATE VariableCursor
					END

				UPDATE TOP (1) @Aux SET IdTipoVariable = @IdTipoVariableResultado, IdTipoActivoXIndicador = @TipoActivoXIndicadorResultado, Valor = @ValorResultado, Resultado = 4, IdActivo = isnull(@IdActivoResultado, IdEquipo), IdCategoriaActivo = @IdCategoriaActivoResultado WHERE Resultado = @Resultado;
				INSERT INTO ReceptoraMensaje SELECT @FechaHora, @Mensaje, 1, 1;

				DECLARE @IdActivoCheck Bigint
				SELECT TOP (1) @IdActivoCheck = IdActivo FROM @Aux WHERE Resultado = 4
				IF @IdActivoCheck is null 
					BEGIN
						DELETE FROM @Aux WHERE Resultado != 4
						
							DECLARE CursorEquipos CURSOR FOR SELECT IdEquipo FROM Equipo WHERE IdTipoEquipo = 9
								OPEN CursorEquipos;
									FETCH NEXT FROM CursorEquipos INTO @IdActivoResultado;
									WHILE @@FETCH_STATUS = 0
										BEGIN
											INSERT INTO @Aux 
											SELECT TOP (1) IdTipoMensaje ,IdTipoVariable ,IdConsecutivo	,Resultado	,Mensaje ,Valor	,IdTipoActivoXIndicador , @IdActivoResultado , IdCategoriaActivo , DescripcionResultado	, @IdActivoResultado , IdParticion , IdUsuario
											FROM @Aux WHERE Resultado = 4;
											
											FETCH NEXT FROM CursorEquipos INTO @IdActivoResultado;
										END
								CLOSE CursorEquipos;
								DEALLOCATE CursorEquipos;
					END

		END
		INSERT INTO ReceptoraMensajeDetalle (FechaHora, IdTipoMensaje, IdTipoVariable, IdConsecutivo,  Resultado,Mensaje, Valor, IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado) 
		SELECT TOP 1 @FechaHora, IdTipoMensaje, IdTipoVariable, IdConsecutivo,  Resultado,Mensaje, Valor, IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado FROM @Aux;
	END
	SELECT IdTipoMensaje,IdTipoVariable	,IdConsecutivo	,Resultado	,Mensaje	,Valor	,IdTipoActivoXIndicador , IdActivo , IdCategoriaActivo , DescripcionResultado, IdParticion, IdUsuario FROM @Aux WHERE Resultado = 4 AND IdActivo is not null and IdtipoActivoXIndicador is not null
END

-- ACTUAL

GO
ALTER procedure [dbo].[ReceptoraProcesarMensaje] @Mensaje varchar(100), @FechaHora datetime
as
begin
	declare @Aux TABLE (IdTipoMensaje	char(1),IdTipoVariable	bigint,IdConsecutivo	smallint,Resultado	smallint,Mensaje	varchar(100),Valor	varchar(50),IdTipoActivoXIndicador int, IdActivo bigint, IdCategoriaActivo smallint, DescripcionResultado	varchar(500), IdEquipo bigint, IdParticion varchar(5), IdUsuario varchar(5))
	declare @IdTipoVariableDefecto bigint = 9999
	declare @ResultadoDefecto smallint = 4
	declare @ValorDefecto varchar(50) = '5'
	declare @TipoActivoXIndicadorReceptoraDefecto int = 3837
	declare @IdTipoACtivoXIndicadorCentralAlarmaDefecto int = 3838
	declare @IdCategoriaActivoDefecto smallint = 0
	if (NOT exists (SELECT top 1 * FROM ReceptoraMensajeExcluido WHERE Mensaje = @Mensaje) )
	begin
		INSERT INTO @Aux SELECT * FROM ProcesarMensajeReceptora(@Mensaje) WHERE Resultado IN (3,4,5);
		if (exists (SELECT top 1 Resultado FROM @Aux WHERE Resultado = 4))
		begin
			INSERT INTO ReceptoraMensaje SELECT @FechaHora, @Mensaje, 1, 0;
		end
		else
		begin
			UPDATE TOP (1) @Aux SET IdTipoVariable = @IdTipoVariableDefecto, IdTipoActivoXIndicador = @IdTipoACtivoXIndicadorCentralAlarmaDefecto, Valor = '1', Resultado = 4, IdActivo = IdEquipo, IdCategoriaActivo = @IdCategoriaActivoDefecto WHERE Resultado = 3
			if(NOT exists( SELECT top 1 Resultado FROM @Aux WHERE Resultado = 4))
			begin
				DECLARE @Id int
				DECLARE MY_CURSOR CURSOR READ_ONLY STATIC READ_ONLY FORWARD_ONLY FOR SELECT DISTINCT IdEquipo FROM Equipo WHERE IdTipoEquipo = 9
				OPEN MY_CURSOR
				FETCH NEXT FROM MY_CURSOR INTO @Id
				WHILE @@FETCH_STATUS = 0
				BEGIN 
					INSERT INTO @Aux (IdTipoMensaje, IdTipoVariable, IdConsecutivo, Resultado, Mensaje, Valor , IdTipoActivoXIndicador , IdActivo, IdCategoriaActivo , DescripcionResultado) 
					(SELECT TOP (1) IdTipoMensaje, @IdTipoVariableDefecto, IdConsecutivo,  @ResultadoDefecto,Mensaje, '5', @TipoActivoXIndicadorReceptoraDefecto, @Id, @IdCategoriaActivoDefecto, DescripcionResultado FROM @Aux WHERE Resultado = 5)
					FETCH NEXT FROM MY_CURSOR INTO @Id
				END
				CLOSE MY_CURSOR
				DEALLOCATE MY_CURSOR
			end
			INSERT INTO ReceptoraMensaje SELECT @FechaHora, @Mensaje, 1, 1;
		end
		INSERT INTO ReceptoraMensajeDetalle (FechaHora, IdTipoMensaje, IdTipoVariable, IdConsecutivo,  Resultado,Mensaje, Valor, IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado) 
		SELECT TOP 1 @FechaHora, IdTipoMensaje, IdTipoVariable, IdConsecutivo,  Resultado,Mensaje, Valor, IdTipoActivoXIndicador, IdActivo, IdCategoriaActivo, DescripcionResultado FROM @Aux;
	end
	SELECT IdTipoMensaje,IdTipoVariable	,IdConsecutivo	,Resultado	,Mensaje	,Valor	,IdTipoActivoXIndicador , IdActivo , IdCategoriaActivo , DescripcionResultado, IdParticion, IdUsuario FROM @Aux WHERE Resultado = 4
end
GO
