<properties
	pageTitle="Come eseguire la migrazione di app per la logica alla versione dello schema 2015-08-01-preview | Servizio app di Microsoft Azure"
	description="È possibile eseguire facilmente la migrazione di app per la logica alla versione più recente dello schema, seguendo questa procedura."
	services="logic-apps"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="deonhe"/>

# Come eseguire la migrazione di app per la logica alla versione dello schema 2015-08-01-preview

Per spostare le app per la logica esistenti nel nuovo schema, seguire questa procedura:
1. Aprire l'app per la logica nel portale di Azure.
2. Fare clic su Aggiorna schema:

![Icona API][step1]

Viene visualizzata la pagina Aggiorna schema, con un collegamento a un documento che fornisce dettagli sui miglioramenti disponibili nel nuovo schema:

![Icona API][step2]

>[AZURE.NOTE] Quando si seleziona **Aggiorna schema**, vengono eseguiti automaticamente i passaggi della migrazione e viene generato l'output del codice. È possibile usare questi elementi per aggiornare la definizione. Assicurarsi, tuttavia, di seguire le procedure di codifica consigliate, ad esempio quelle indicate nella **sezione seguente**.

## Procedure consigliate durante la migrazione di app per la logica alla versione più recente dello schema  

- Copiare lo script di cui è stata eseguita la migrazione in una nuova app per la logica. Non sovrascrivere l'app precedente fino a quando non è stato completato il test ed è stato confermato il corretto funzionamento dell'app di cui è stata eseguita la migrazione.
- Testare l'app per la logica **prima** di passare alla fase di produzione.
- Al termine della migrazione, avviare l'aggiornamento delle app per la logica per usare le [API gestite](./apis-list.md), quando è possibile. Ad esempio, si può iniziare a usare Dropbox 2 anziché Dropbox 1.


## Passaggi successivi
-  [Informazioni su come eseguire manualmente la migrazione delle app per la logica](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!---HONumber=AcomDC_0824_2016-->