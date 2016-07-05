<properties
  pageTitle="Domande frequenti su Azure IoT Suite | Microsoft Azure"
  description="Domande frequenti su IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="06/27/2016"
  ms.author="araguila"/>
   
# Domande frequenti su IoT Suite

### Che differenza c'è tra eliminare un gruppo di risorse nel portale di Azure e fare clic per eliminare una soluzione preconfigurata in azureiotsuite.com?

- Eliminando la soluzione preconfigurata in [azureiotsuite.com][lnk-azureiotsuite], vengono eliminate tutte le risorse di cui è stato eseguito il provisioning durante la creazione della soluzione preconfigurata. Vengono eliminate anche altre eventuali risorse aggiunte al gruppo di risorse.

- Eliminando il gruppo di risorse nel [portale di Azure][lnk-azure-portal], vengono eliminate solo le risorse presenti nel gruppo di risorse. È necessario eliminare anche l'applicazione Azure Active Directory associata alla soluzione preconfigurata nel [portale di Azure classico][lnk-classic-portal].

### Di quante istanze di DocumentDB è possibile eseguire il provisioning in una sottoscrizione?

Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure][link-azuresupportticket] per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di cinquanta istanze di DocumentDB per ogni sottoscrizione. Di conseguenza, è possibile eseguire il provisioning di un massimo di cinque soluzioni preconfigurate per il monitoraggio remoto in una determinata sottoscrizione.

### Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione per il monitoraggio remoto viene effettuato con il piano Transazioni sito Web interno - Livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### Se si usa la distribuzione di soluzioni di monitoraggio remoto con una mappa statica, come si aggiunge una mappa di Bing interattiva? 
1. È possibile ottenere la chiave QueryKey di Bing Maps API for Enterprise dal [portale di Azure][lnk-azure-portal]\:
 1. Passare al gruppo di risorse in cui si trova Bing Maps API for Enterprise nel [portale di Azure][lnk-azure-portal].
 2. Fare clic su Tutte le impostazioni, quindi su Gestione chiavi.
 3. Si noteranno due chiavi: MasterKey e QueryKey. Copiare il valore per QueryKey.

     > [AZURE.NOTE] Se non si ha un account Bing Maps API for Enterprise, è possibile crearne uno nel [portale di Azure][lnk-azure-portal]. A questo scopo fare clic su + Nuovo, cercare Bing Maps API for Enterprise e seguire i prompt per la creazione.

2. Ottenere il codice più recente da [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Eseguire una distribuzione locale o cloud in base alle indicazioni per la distribuzione da riga di comando seguenti nella cartella /docs/ nel repository.

4. Dopo aver eseguito una distribuzione locale o cloud, cercare nella cartella radice il file *.user.config creato durante la distribuzione. Aprire il file in un editor di testo.

5. Modificare la riga seguente in modo da includere il valore copiato da QueryKey:
   
  `<setting name="MapApiQueryKey" value="" />`

### È possibile creare una soluzione preconfigurata se è disponibile Microsoft Azure per DreamSpark?
Al momento non è possibile creare una soluzione preconfigurata con un account [Microsoft Azure per DreamSpark][lnk-dreamspark]. Tuttavia, è possibile creare un [account di valutazione gratuito per Azure][lnk-30daytrial] in pochi minuti che consentirà di creare una soluzione preconfigurata.

### Come si elimina un tenant AAD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD][lnk-delete-aad-tennant].

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0629_2016-->