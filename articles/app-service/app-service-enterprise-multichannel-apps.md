<properties 
	pageTitle="App multicanale aziendali" 
	description="Panoramica di come un'app multicanale si estende alle risorse locali e ai servizi software basati sul cloud." 
	services="app-service" 
	documentationCenter="na" 
	authors="stefsch" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="stefsch"/>

# Creare applicazioni multicanale per l'organizzazione

## Panoramica

Le app multicanale aziendali presentano i dati ai clienti usando più tecnologie. Gli utenti Web aziendali, mobili e API recuperano ed elaborano le informazioni provenienti da diverse origini. Queste fonti si estendono ai sistemi interni eseguiti in locale nonché ai servizi basati sul cloud.

Le applicazioni aziendali hanno inoltre il requisito dell'accesso sicuro per consentire a dipendenti e partner aziendali di connettersi ai dati usando identità sicure sotto il controllo diretto dell'organizzazione.

Le applicazioni aziendali in esecuzione sul servizio app di Azure possono fornire tutte queste funzionalità.

L'applicazione di esempio relativa al viaggio di un dipendente riportata sotto mostra un'applicazione multicanale aziendale protetta con Azure Active Directory (AAD) che si integra con le risorse locali nonché con i servizi SaaS (Software-as-a-Service, software come un servizio) quali Office 365 e Salesforce.

## <a name="acceptablefiles"></a>Controllo dell'accesso con Azure Active Directory

Gli utenti dell'applicazione di viaggio devono innanzitutto autenticarsi tramite Active Directory aziendale. Sono stati eseguiti alcuni passaggi per configurare l'applicazione per l'uso di Azure Active Directory (AAD):

* È stata creata un'istanza di Azure Active Directory per l'organizzazione.
* L'istanza Active Directory locale dell'organizzazione è stata federata con Azure Active Directory.
* Infine, l'applicazione è stata registrata con AAD usando la semplice funzionalità di integrazione AAD del servizio app di Azure. 

Come risultato finale l'applicazione richiede agli utenti di effettuare l'accesso con AAD (e, di conseguenza, con l'istanza Active Directory aziendale).
	
![Account di accesso AAD][AADLogin]

Per altre informazioni sulla configurazione dell'integrazione del servizio app di Azure con AAD, vedere l'articolo relativo all'[integrazione del servizio app di Azure con AAD][AASIntegrationwithAAD].

## <a name="acceptablefiles"></a>Accesso alle risorse locali

Dopo che un utente ha effettuato l'accesso ad AAD, viene visualizzato un elenco di viaggi aziendali pianificati. Poiché l'applicazione Web è in esecuzione in Microsoft Azure, è necessario poter accedere al server SQL locale che contiene queste informazioni.

![Dati da Server SQL locale][DatafromOnpremisesSqlServer]

L'applicazione è configurata con la funzionalità di integrazione di reti virtuali point-to-site. Ciò consente all'applicazione di usare la logica di accesso ai dati standard (in questo caso, Entity Framework) per accedere in modo trasparente a un server SQL remoto in esecuzione all'interno della rete aziendale.

Per altre informazioni sull'integrazione di reti virtuali point-to-site, vedere l'articolo relativo all'[integrazione di reti virtuali][VNETIntegration].

## <a name="acceptablefiles"></a>Integrazione con Office 365

Ogni volta che un dipendente crea un record di viaggio, l'applicazione Web crea una richiesta di viaggio in un elenco SharePoint Online. Nell'applicazione è presente un collegamento che i dipendenti possono usare per accedere facilmente all'elenco SharePoint:

![Collegamento dell'elenco SharePoint][SharepointListLink]

Selezionando il collegamento verrà visualizzato automaticamente l'elenco SharePoint. Poiché i dipendenti hanno effettuato l'accesso con le proprie AAD aziendali, vengono autenticati in modo trasparente a Office 365 usando il token di sicurezza rilasciato da AAD.

![Elenco SharePoint][SharepointList]

L'applicazione Web crea inoltre un documento di viaggio in una raccolta documenti SharePoint Online.

![Raccolta documenti di SharePoint][SharepointDocumentLibrary]

Le risorse create in SharePoint Online consentono all'applicazione Web di sfruttare le funzionalità di Office 365. Ad esempio un flusso di lavoro di approvazione/decisione può essere attivato ogni volta che viene creato un elemento nell'elenco SharePoint.

Per altre informazioni sull'integrazione di Office 365, vedere l'articolo relativo all'[integrazione di Office 365][Office365Integration].

## <a name="acceptablefiles"></a>Integrazione con i servizi SaaS

Le aziende oggi usano un'ampia gamma di servizi SaaS e hanno l'esigenza di interagire con i dati di altre applicazioni SaaS. L'applicazione di viaggio mostra un esempio di questo scenario. Aggiorna le informazioni account dei clienti in Salesforce ogni volta che un dipendente pianifica un viaggio di visita a un cliente.

![Integrazione di Salesforce][SalesforceIntegration]

L'account Salesforce dell'azienda è configurato con AAD per consentire l'autenticazione trasparente con Salesforce usando le credenziali AAD. Di conseguenza, dopo che i dipendenti effettuano l'accesso all'applicazione di viaggio usando AAD, l'applicazione può leggere e scrivere i dati archiviati in Salesforce.

Per altre informazioni sull'integrazione di SaaS, vedere l'articolo relativo all'[integrazione SaaS][SaaSIntegration].

## <a name="NextSteps"></a>Passaggi successivi

Per altre informazioni, vedere l'articolo relativo ai [servizi delle applicazioni Azure][AzureApplicationServices].
 
[AASIntegrationwithAAD]: http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/
[VNETIntegration]: http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/
[Office365Integration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-office365/
[SaaSIntegration]: http://azure.microsoft.com/documentation/articles/app-service-logic-connector-salesforce/
[AzureApplicationServices]: ../app-service-cloud-app-platform.md

[AADLogin]: ./media/app-service-enterprise-multichannel-apps/01aAADLogin.png
[DatafromOnpremisesSqlServer]: ./media/app-service-enterprise-multichannel-apps/02aDatafromOnpremisesSqlServer.png
[SharepointListLink]: ./media/app-service-enterprise-multichannel-apps/03aSharepointListLink.png
[SharepointList]: ./media/app-service-enterprise-multichannel-apps/04aSharepointList.png
[SharepointDocumentLibrary]: ./media/app-service-enterprise-multichannel-apps/05aSharepointDocumentLibrary.png
[SalesforceIntegration]: ./media/app-service-enterprise-multichannel-apps/06aSalesforceIntegration.png

<!---HONumber=August15_HO6-->