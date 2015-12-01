<properties
  pageTitle="Domande frequenti su Azure IoT Suite | Microsoft Azure"
  description="Domande frequenti su IoT Suite."
  services=""
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="na"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# Domande frequenti

### Di quante istanze di DocumentDB è possibile eseguire il provisioning in una sottoscrizione?

Cinque. È possibile creare un ticket di supporto per aumentare questo limite, ma per impostazione predefinita è possibile eseguire il provisioning solo di cinque istanze di DocumentDB per sottoscrizione. Di conseguenza, è possibile eseguire il provisioning di un massimo di cinque soluzioni preconfigurate per il monitoraggio remoto in una determinata sottoscrizione.

### Di quante API di Bing Maps gratuite è possibile eseguire il provisioning in una sottoscrizione?

Due. È possibile creare solo due API di Bing Maps gratuite in una sottoscrizione. Per impostazione predefinita il provisioning della soluzione per il monitoraggio remoto include un'API di Bing Maps gratuita. Di conseguenza, è possibile eseguire il provisioning di un massimo di due soluzioni per il monitoraggio remoto in una sottoscrizione senza modifiche.

### Che differenza c'è tra eliminare un gruppo di risorse nel portale di anteprima di Azure e fare clic per eliminare una soluzione preconfigurata in azureiotsuite.com?

- Eliminando la soluzione preconfigurata in [azureiotsuite.com][lnk-azureiotsuite], vengono eliminate tutte le risorse di cui è stato eseguito il provisioning durante la creazione della soluzione preconfigurata. Vengono eliminate anche altre eventuali risorse aggiunte al gruppo di risorse. 

- Eliminando il gruppo di risorse nel [portale di anteprima di Azure][lnk-azure-portal], vengono eliminate solo le risorse presenti nel gruppo di risorse. È necessario eliminare anche l'applicazione Azure Active Directory associata alla soluzione preconfigurata nel [portale di gestione di Azure][lnk-classic-portal].

### Come si elimina un tenant AAD?

Vedere il post del blog di Eric Golpe relativo alla [procedura dettagliata di eliminazione di un tenant di Azure AD][lnk-delete-aad-tennant].


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_1125_2015-->