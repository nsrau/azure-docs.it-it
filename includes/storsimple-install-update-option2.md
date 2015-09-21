<properties 
   pageTitle="Opzione 2: Utilizzare il portale di gestione di Azure per applicare l'aggiornamento 1.2"
   description="Viene illustrato come utilizzare il portale di gestione per installare l'aggiornamento 1.2 della serie 8000 di StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/08/2015"
   ms.author="v-sharos" />

#### Per installare l'aggiornamento 1.2 dal portale di gestione di Azure

1. Nel portale di gestione, accedere alla pagina **Dispositivi** e selezionare il proprio dispositivo.
 
2. Andare a **Dispositivi** > **Configura**.

3. In **interfacce di rete**, verificare innanzitutto di disporre di almeno un’interfaccia di rete abilitata per iSCSI. Poi individuare l'interfaccia di rete (diversa da DATA 0) con gateway assegnato.

4. Disabilitare l'interfaccia di rete con un gateway assegnato e salvare la configurazione modificata. Si noti che vengono mantenute le impostazioni dell'interfaccia di rete e pertanto quando si riattiva questa interfaccia di rete in un secondo momento, il portale consentirà di ripristinare le impostazioni originali.

7. A questo punto è possibile [utilizzare il portale di gestione per installare l'aggiornamento 1.2](#use-the-management-portal-to-install-update-1). Seguire le istruzioni a partire dal passaggio 3 di questa procedura. Dopo aver installato tutti gli aggiornamenti, è possibile riabilitare l'interfaccia di rete che è stata disabilitata.

<!---HONumber=Sept15_HO2-->