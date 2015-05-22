<properties 
	pageTitle="Conector Slack"
	description="Introducción al conector Slack"
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="andalmia"/>

# Uso del conector Slack en la aplicación lógica #

Las aplicaciones lógicas se pueden desencadenar en función de una variedad de orígenes de datos y ofrecen conectores para obtener y procesar los datos como parte del flujo.

Un conector Slack le permite publicar mensajes en canales Slack.

## Creación de un conector Slack para la aplicación lógica ##
Para usar el conector Slack, deberá crear primero una instancia de la aplicación de API del conector Slack. Se puede hacer de la forma siguiente:

1.	Abra Azure Marketplace mediante la opción "+ NUEVO" en la parte inferior izquierda del Portal de Azure.
2.	Vaya a “Web y móvil \> Azure Marketplace” y busque “Conector Slack”.
3.	Configure el conector Slack de la siguiente forma:
 
	![][1] 
	- **Nombre**: asigne un nombre al conector Slack. 
	- **Plan del Servicio de aplicaciones**: seleccione o cree un plan del Servicio de aplicaciones. 
	- **Nivel de precios**: elija un nivel de precios para el conector. 
	- **Grupo de recursos**: seleccione o cree un grupo de recursos donde debe residir el conector. 
	- **Suscripción**: elija la suscripción en la que desea crear el conector. 
	- **Ubicación**: elija la ubicación geográfica donde desea que se implemente el conector.

4. Haga clic en Crear. Se creará un nuevo conector Slack.
5. Una vez creada la instancia de aplicación de la API, puede crear una aplicación lógica en el mismo grupo de recursos para usar el conector Slack.

## Uso del conector Slack en la aplicación lógica ##
Una vez creada la aplicación de la API, ahora puede usar el conector Slack como acción para la aplicación lógica. Para ello, necesita lo siguiente:

1.	Cree una nueva aplicación lógica y elija el mismo grupo de recursos que tiene el conector Slack. Siga las instrucciones para [crear una nueva aplicación lógica].  	
	
2.	Abra “Desencadenadores y acciones” dentro de la aplicación lógica creada para abrir el Diseñador de aplicaciones lógicas y configure el flujo.
	
3.	El conector Slack aparecerá en la sección “Aplicaciones de API en este grupo de recursos” en la galería, en el lado derecho.
 
	![][2]
4.	Puede quitar la aplicación de la API del conector Slack en el editor haciendo clic en “Conector Slack”. Haga clic en el botón Autorizar. Proporcione las credenciales de Microsoft \(si no ha iniciado sesión automáticamente\). Inicie sesión en su cuenta Slack mediante los pasos correspondientes. Hacia el final, se le pedirá que conceda al conector permiso de acceso a su cuenta Slack. Haga clic en "Autorizar"
 
	![][3]
	![][4]
	![][5]
	![][6]
	
5.	Ahora puede usar el conector Slack en el flujo. Actualmente, los desencadenadores no están disponibles en el conector Slack. Las acciones disponibles son: Publicar mensaje
 
	![][7]

6.	Vamos a explicar el proceso para "Publicar mensaje". Puede usar esta acción para publicar un mensaje en cualquier canal Slack.
 
	![][8]

	Configure las propiedades de entrada de la acción "Publicar mensaje" de la siguiente manera:

 - **Texto**: especifique el texto del mensaje que se va a publicar.
 - **Nombre de canal**: especifique el canal Slack en el que se va a cargar este mensaje. Si no se proporciona ninguno, el mensaje se publicarán en \#general.

 	**Propiedades avanzadas**
 	- **Nombre de usuario de bot**: nombre del bot que se va a usar para este mensaje. Si no se especifica, los mensajes se publicarán como "Bot".
 	- **URL de icono**: dirección URL de una imagen que se va a usar como icono del mensaje.
 	- **Emoji icono**: emoji que se va a usar como icono del mensaje. Reemplaza a URL de icono.
 

7. Para usar el conector fuera de una aplicación lógica, pueden aprovecharse las API de REST expuestas por el conector. Puede ver estas definiciones de API con Examinar -\> Aplicación de API -\> Conector Slack. Ahora haga clic en el modo de definición de API en la sección de resumen para ver todas las API expuestas por este conector.

	![][9]

9. También puede encontrar detalles de las API en la [definición de la API de Slack].

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!-- Links -->
[crear una nueva aplicación lógica]: app-service-logic-create-a-logic-app.md
[definición de la API de Slack]: https://msdn.microsoft.com/es-es/library/dn708020.aspx
<!--HONumber=52-->
