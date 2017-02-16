---
title: Domande frequenti su Azure IoT Suite | Microsoft Docs
description: Domande frequenti su IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: aguilaaj
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 69d4439a20fec8caaabdb43444cabd81f7d1b7c8
ms.openlocfilehash: 6245c2a0f73ff63e3bfb5fe16112f89ed0efdf30


---
# <a name="frequently-asked-questions-for-iot-suite"></a>Domande frequenti su IoT Suite
### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Che differenza c'è tra eliminare un gruppo di risorse nel portale di Azure e fare clic per eliminare una soluzione preconfigurata in azureiotsuite.com?
* Se si elimina la soluzione preconfigurata in [azureiotsuite.com][lnk-azureiotsuite], si eliminano anche tutte le risorse di cui è stato eseguito il provisioning al momento della creazione della soluzione. Se sono state aggiunte altre risorse al gruppo, anche queste ultime vengono eliminate. 
* Se si elimina il gruppo di risorse nel [portale di Azure][lnk-azure-portal], si eliminano solo le risorse presenti in tale gruppo. È anche necessario eliminare l'applicazione Azure Active Directory associata alla soluzione preconfigurata nel [portale di Azure classico][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Di quante istanze dell'hub IoT è possibile eseguire il provisioning in una sottoscrizione?
Dieci. Anche se è possibile creare un [ticket di supporto di Azure][link-azuresupportticket] per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 10 hub IoT per ogni sottoscrizione, come descritto in [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi][link-azuresublimits]. Di conseguenza, poiché ogni soluzione preconfigurata effettua il provisioning di un nuovo hub IoT, è possibile effettuare il provisioning solo di un massimo di 10 soluzioni preconfigurate in una determinata sottoscrizione. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Di quante istanze di DocumentDB è possibile eseguire il provisioning in una sottoscrizione?
Cinquanta. Anche se è possibile creare un [ticket di supporto di Azure][link-azuresupportticket] per aumentare questo limite, per impostazione predefinita è possibile effettuare il provisioning solo di 50 istanze di DocumentDB per ogni sottoscrizione. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?
Due. È possibile creare solo due Transazioni sito Web interno - Livello 1 per Bing Maps per i piani aziendali in una sottoscrizione di Azure. Per impostazione predefinita, il provisioning della soluzione per il monitoraggio remoto viene effettuato con il piano Transazioni sito Web interno - Livello 1. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Se si usa la distribuzione di soluzioni di monitoraggio remoto con una mappa statica, come si aggiunge una mappa di Bing interattiva?
1. È possibile ottenere la chiave QueryKey di Bing Maps API for Enterprise dal [portale di Azure][lnk-azure-portal]: 
   
   1. Passare al gruppo di risorse in cui si trova Bing Maps API for Enterprise nel [portale di Azure][lnk-azure-portal].
   2. Fare clic su **All Settings** (Tutte le impostazioni) e quindi su **Key Management** (Gestione chiavi). 
   3. Vengono visualizzate le due chiavi **MasterKey** e **QueryKey**. Copiare il valore per **QueryKey**.
      
      > [!NOTE]
      > Se non si ha un account Bing Maps API for Enterprise, è possibile crearne uno nel [portale di Azure][lnk-azure-portal]. A questo scopo fare clic su +Nuovo, cercare Bing Maps API for Enterprise e seguire le istruzioni per la creazione.
      > 
      > 
2. Ottenere il codice più recente da [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Eseguire una distribuzione locale o cloud in base alle indicazioni per la distribuzione da riga di comando nella cartella /docs/ del repository. 
4. Dopo aver eseguito una distribuzione locale o cloud, cercare nella cartella radice il file *.user.config creato durante la distribuzione. Aprire il file in un editor di testo. 
5. Modificare la riga seguente in modo da includere il valore copiato da **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>È possibile creare una soluzione preconfigurata se è disponibile Microsoft Azure per DreamSpark?
Al momento non è possibile creare una soluzione preconfigurata con un account [Microsoft Azure for DreamSpark][lnk-dreamspark], ma è possibile creare facilmente un [account di valutazione gratuito per][lnk-30daytrial] che consente di creare una soluzione preconfigurata.

### <a name="how-do-i-delete-an-aad-tenant"></a>Come si elimina un tenant AAD?
Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Passaggi successivi
È anche possibile esplorare alcune altre funzionalità delle soluzioni preconfigurate di IoT Suite:

* [Panoramica della soluzione preconfigurata di manutenzione predittiva][lnk-predictive-overview]
* [Sicurezza IoT sin dall'inizio][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Jan17_HO1-->


