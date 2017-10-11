---
title: Ripristino di emergenza per l'array virtuale StorSimple e failover del dispositivo | Microsoft Docs
description: "Scoprire di più su come eseguire il failover di StorSimple Virtual Array."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Ripristino di emergenza e failover del dispositivo per l'array virtuale StorSimple tramite il portale di Azure

## <a name="overview"></a>Panoramica
Questo articolo descrive il ripristino di emergenza per l'array virtuale Microsoft Azure StorSimple, inclusi i passaggi dettagliati per eseguire il failover a un altro array virtuale. Il failover consente di spostare i dati da un dispositivo di *origine* nel data center a un dispositivo di *destinazione*. Il dispositivo di destinazione può trovarsi nella stessa o in un'altra area geografica. Il failover del dispositivo interessa tutto il dispositivo. Durante il failover, la proprietà dei dati del cloud passa dal dispositivo di origine al dispositivo di destinazione.

Il contenuto di questo articolo si applica solo a StorSimple Virtual Array. Per eseguire il failover di un dispositivo serie 8000, andare a [Failover e ripristino di emergenza per il dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Che cos'è failover del dispositivo e il ripristino di emergenza?

In uno scenario di ripristino di emergenza, il dispositivo principale smette di funzionare. In questo scenario è possibile spostare i dati del cloud associati al dispositivo guasto in un altro dispositivo. È possibile usare il dispositivo primario come *origine* e specificare un altro dispositivo come *destinazione*. Questo processo viene definito *failover*. Durante il failover, tutti i volumi e le condivisioni del dispositivo di origine cambiano proprietà e vengono trasferiti al dispositivo di destinazione. Non è consentito filtrare i dati.

Il ripristino di emergenza è progettato come un ripristino completo del dispositivo tramite la suddivisione in livelli e il rilevamento basati su mappa termica. Una mappa termica viene definita assegnando un valore termico ai dati in base a modelli di lettura e scrittura. Questa mappa termica suddivide in livelli i blocchi di dati con valore termico più basso prima nel cloud, mantenendo i blocchi di dati con valore termico più alto (più usati) a livello locale. Durante un ripristino di emergenza, StorSimple usa la mappa termica per ripristinare e riattivare i dati dal cloud. Il dispositivo recupera tutti i volumi o le condivisioni dal backup più recente (come determinato internamente) ed esegue un ripristino a partire da quel backup. L'array virtuale orchestra l'intero processo di ripristino di emergenza.

> [!IMPORTANT]
> Il dispositivo di origine viene eliminato al termine del failover del dispositivo e quindi il failback non è supportato.
> 
> 

Il ripristino di emergenza viene gestito tramite la funzionalità di failover del dispositivo e viene avviato nel pannello **Dispositivi**. Questo pannello riporta tutti i dispositivi StorSimple connessi al servizio Gestione dispositivi StorSimple. Per ogni dispositivo è possibile esaminare nome descrittivo, stato, capacità fornita e massima, tipo e modello.

## <a name="prerequisites-for-device-failover"></a>Prerequisiti per il failover del dispositivo

### <a name="prerequisites"></a>Prerequisiti

Per il failover del dispositivo, verificare che siano soddisfatti i prerequisiti seguenti:

* Il dispositivo di origine deve essere in stato **Disattivato** .
* Il dispositivo di destinazione deve essere visualizzato come **Pronto per la configurazione** nel portale di Azure. È necessario eseguire il provisioning di un array virtuale di destinazione di capacità pari o superiore. Usare l'interfaccia utente Web locale per configurare e registrare correttamente l'array virtuale di destinazione.
  
  > [!IMPORTANT]
  > Non tentare di configurare il dispositivo virtuale registrato tramite il servizio. Non si deve eseguire nessuna configurazione del dispositivo tramite questo servizio.
  > 
  > 
* Il nome del dispositivo di destinazione non può essere lo stesso del dispositivo di origine.
* Il dispositivo di origine e quello di destinazione devono essere dello stesso tipo. Il failover di un array virtuale configurato come file server può essere eseguito solo su un altro file server. Lo stesso vale per un server iSCSI.
* Per il ripristino di emergenza di un file server, è consigliabile aggiungere il dispositivo di destinazione nello stesso dominio del dispositivo di origine. Questa configurazione assicura che le autorizzazioni di condivisione vengano risolte automaticamente. È supportato solo il failover su un dispositivo di destinazione nello stesso dominio.
* I dispositivi di destinazione disponibili per il ripristino di emergenza sono dispositivi dotati di capacità uguale o superiore rispetto al dispositivo di origine. I dispositivi connessi al servizio che non soddisfano i criteri di spazio sufficiente non sono disponibili come dispositivi di destinazione.

### <a name="other-considerations"></a>Altre considerazioni

* Per un failover pianificato 
  
  * Si consiglia di portare offline tutti i volumi o tutte le condivisioni nel dispositivo di origine.
  * È consigliabile eseguire un backup del dispositivo, quindi procedere con il failover per ridurre al minimo la perdita dei dati. 
* Per un failover non pianificato, il dispositivo usa il backup più recente per ripristinare i dati.

### <a name="device-failover-prechecks"></a>Verifiche preliminari del failover del dispositivo

Prima di iniziare il ripristino di emergenza, il dispositivo esegue le verifiche preliminari. Queste verifiche consentono di ridurre il rischio di errori all'avvio del ripristino di emergenza. Le verifiche preliminari includono:

* Convalida dell'account di archiviazione.
* Controllo della connettività del cloud in Azure.
* Controllo dello spazio disponibile nel dispositivo di destinazione.
* Per un volume di un dispositivo di origine del server iSCSI verifica della validità
  
  * dei nomi relativi a record di controllo di accesso validi
  * di IQN (lunghezza non superiore a 220 caratteri)
  * e di password CHAP (lunghezza di 12 e 16 caratteri).

Se una qualsiasi delle verifiche preliminari riportate sopra non va a buon fine, non è possibile procedere con il ripristino di emergenza. Risolvere questi problemi, quindi riprovare il ripristino di emergenza.

Se il ripristino di emergenza è stato completato correttamente, la proprietà dei dati cloud sul dispositivo di origine viene trasferita al dispositivo di destinazione. Il dispositivo di origine non è quindi più disponibile nel portale. L'accesso a tutti i volumi o tutte le condivisioni sul dispositivo di origine è bloccato e il dispositivo di destinazione diventa attivo.

> [!IMPORTANT]
> Anche se il dispositivo non è più disponibile, la macchina virtuale sottoposta a provisioning nel sistema host usa ancora risorse. Se il ripristino di emergenza è stato completato correttamente, è possibile eliminare questa macchina virtuale dal sistema host.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Eseguire il failover su un array virtuale

È consigliabile eseguire il provisioning, configurare e registrare un altro array virtuale StorSimple con il servizio Gestione dispositivi StorSimple prima di eseguire questa procedura.

> [!IMPORTANT]
> 
> * Non è possibile eseguire il failover da un dispositivo StorSimple serie 8000 a un dispositivo virtuale 1200.
> * È possibile eseguire il failover da un dispositivo virtuale abilitato FIPS (Federal informazioni Processing Standard) a un altro dispositivo abilitato FIPS o a un dispositivo non FIPS distribuito nel portale di Azure dedicato agli enti pubblici.


Eseguire i passaggi seguenti per ripristinare il dispositivo su un dispositivo virtuale StorSimple di destinazione.

1. Eseguire il provisioning e configurare un dispositivo di destinazione che soddisfi i [prerequisiti per il failover del dispositivo](#prerequisites). Completare la configurazione del dispositivo tramite l'interfaccia utente Web locale e la registrazione al servizio Gestione dispositivi StorSimple. Se si crea un file server, andare al passaggio 1 di [Eseguire la configurazione come file server](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Se si crea un server iSCSI, andare al passaggio 1 di [Eseguire la configurazione come server iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Portare i volumi o le condivisioni offline nell'host. Vedere le istruzioni specifiche del sistema operativo per l'host per portare offline i volumi o le condivisioni. Se non è ancora offline, è necessario portare offline tutti i volumi o tutte le condivisioni nel dispositivo eseguendo le operazioni seguenti.
   
    1. Passare al pannello **Dispositivi** e selezionare il dispositivo.
   
    2. Passare a **Impostazioni > Gestisci > Condivisioni** o **Impostazioni > Gestisci > Volumi**. 
   
    3. Selezionare un volume o una condivisione, fare clic con il pulsante destro del mouse e selezionare **Porta offline**. 
   
    4. Alla richiesta di conferma, selezionare **Sono consapevole dell'impatto che può conseguire dall'azione di portare offline questa condivisione.** 
   
    5. Fare clic su **Porta offline**.

3. Nel servizio Gestione dispositivi StorSimple passare a **Gestione > Dispositivi**. Nel pannello **Dispositivi** selezionare e fare clic sul dispositivo di origine.

4. Nel pannello **Dashboard dispositivo** fare clic su **Disattiva**.

5. Nel pannello **Disattiva** viene chiesta la conferma. La disattivazione del dispositivo è un processo *permanente* che non può essere annullato. Viene inoltre ricordato di portare offline le condivisioni o i volumi sull'host. Digitare il nome del dispositivo per confermare e fare clic su **Disattiva**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Verrà avviata la disattivazione. Se la disattivazione è stata completata correttamente, si riceverà una notifica.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Nella pagina Dispositivi lo stato del dispositivo viene modificato in **Disattivato**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Nel pannello **Dispositivi** selezionare e fare clic sul dispositivo di origine disattivato per il failover. 
9. Nel pannello **Dashboard dispositivo** fare clic su **Effettua il failover**. 
10. Nel pannello **Effettua il failover del dispositivo** eseguire le operazioni seguenti:
    
    1. Il campo del dispositivo di origine viene popolato automaticamente. Si noti la dimensione totale dei dati per il dispositivo di origine. La dimensione dei dati deve essere inferiore alla capacità disponibile sul dispositivo di destinazione. Esaminare i dettagli associati al dispositivo di origine, ad esempio il nome del dispositivo, la capacità totale e i nomi delle condivisioni di cui è stato eseguito il failover.

    2. Nell'elenco a discesa dei dispositivi disponibili scegliere un **Dispositivo di destinazione**. Solo i dispositivi dotati di capacità sufficiente vengono visualizzati nell'elenco a discesa.

    3. Selezionare **Sono consapevole che questa operazione effettuerà il failover dei dati nel dispositivo di destinazione**. 

    4. Fare clic su **Effettua il failover**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Viene avviato un processo di failover e si riceverà una notifica. Passare a **Dispositivi > Processi** per monitorare il failover.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Nel pannello **Processi** viene visualizzato un processo di failover creato per il dispositivo di origine. Questo processo esegue le verifiche preliminari per il ripristino di emergenza.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Se le verifiche preliminari per il ripristino di emergenza hanno esito positivo, il processo di failover distribuisce i processi di ripristino per ogni condivisione o volume presente nel dispositivo di origine.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Al termine del failover passare alla pagina **Dispositivi**.
    
    1. Selezionare e fare clic sul dispositivo StorSimple usato come dispositivo di destinazione per il processo di failover.
    2. Passare a **Impostazioni > Gestione > Condivisioni** o **Volumi** in caso di server iSCSI. Nel pannello **Condivisioni** è possibile visualizzare tutte le condivisioni (volumi) del dispositivo precedente.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Sarà necessario [creare un alias DNS](https://support.microsoft.com/kb/168322) in modo che tutte le applicazioni che stanno tentando di connettersi possano venire reindirizzate al nuovo dispositivo.

## <a name="errors-during-dr"></a>Errori durante il ripristino di emergenza

**Interruzione della connettività cloud durante il ripristino di emergenza**

Se la connettività cloud viene interrotta dopo l'avvio del ripristino di emergenza e prima del completamento del ripristino del dispositivo, il ripristino di emergenza non va a buon fine. Si riceverà una notifica di errore. Il dispositivo di destinazione per il ripristino di emergenza è contrassegnato come *inutilizzabile*. Non è possibile usare lo stesso dispositivo di destinazione per ripristini di emergenza futuri.

**Nessun dispositivo di destinazione compatibile**

Se i dispositivi di destinazione disponibili non dispongono di spazio sufficiente, viene visualizzato un errore relativo all'assenza di dispositivi di destinazione compatibili.

**Errori nelle verifiche preliminari**

Se non viene soddisfatta una delle verifiche preliminari, vengono visualizzati gli errori corrispondenti.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Ripristino di emergenza di continuità aziendale (BCDR)

Uno scenario di ripristino di emergenza di continuità aziendale (BCDR) si verifica quando l'intero data center di Azure smette di funzionare. Ciò può influire sul servizio Gestione dispositivi StorSimple e sui dispositivi StorSimple associati.

Se sono presenti dispositivi StorSimple registrati prima del verificarsi di un'emergenza, questi dispositivi StorSimple rischiano di dover essere eliminati. Dopo l'emergenza, è possibile ricreare e configurare i dispositivi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [Usare l'interfaccia utente Web per amministrare StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

