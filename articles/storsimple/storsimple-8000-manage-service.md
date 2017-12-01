---
title: Distribuire il servizio Gestione dispositivi StorSimple in Azure | Microsoft Docs
description: Descrive le procedure per creare ed eliminare il servizio Gestione dispositivi StorSimple nel portale di Azure e per gestire la chiave di registrazione del servizio.
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: alkohli
ms.openlocfilehash: 5f31e32bb7cbd747af2e03699cfb2c6418828f8d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Distribuire il servizio Gestione dispositivi StorSimple per i dispositivi StorSimple serie 8000

## <a name="overview"></a>Panoramica

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Dopo aver creato il servizio, è possibile usarlo per gestire tutti i dispositivi connessi al servizio Gestione dispositivi StorSimple da un'unica posizione centrale, con una conseguente riduzione del carico amministrativo.

In questa esercitazione vengono descritti i passaggi necessari per la creazione, l'eliminazione, la migrazione del servizio e la gestione della chiave di registrazione del servizio. Le informazioni contenute in questo articolo si applicano solo ai dispositivi StorSimple serie 8000. Per altre informazioni sugli array virtuali StorSimple, vedere le informazioni su come [distribuire un servizio Gestione dispositivi StorSimple per l'array virtuale StorSimple](storsimple-virtual-array-manage-service.md).

## <a name="create-a-service"></a>Creare un servizio
Per creare un servizio Gestione dispositivi StorSimple, è necessario disporre di:

* Una sottoscrizione con un contratto Enterprise Agreement
* Un account di archiviazione di Microsoft Azure attivo
* Le informazioni di fatturazione usate per la gestione degli accessi

Sono consentite solo le sottoscrizioni con un contratto Enterprise Agreement. Le sottoscrizioni Microsoft Sponsorship che erano consentite nel portale di Azure classico non sono supportate nel portale di Azure. Se si usa una sottoscrizione non supportata, verrà visualizzato il messaggio seguente:

![Sottoscrizione non valida](./media/storsimple-8000-manage-service/subscription-not-valid.jpg)

È inoltre possibile scegliere di generare un account di archiviazione predefinito al momento della creazione del servizio.

Un singolo servizio può gestire più dispositivi, ma un dispositivo non può estendersi a più servizi. Una grande impresa può avere più istanze del servizio per lavorare con diverse sottoscrizioni, organizzazioni o anche percorsi di distribuzione. 

> [!NOTE]
> Per gestire dispositivi StorSimple serie 8000 e array virtuali StorSimple sono necessarie istanze separate del servizio Gestione dispositivi StorSimple.

Per creare un servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Per ogni servizio Gestione dispositivi StorSimple, sono disponibili gli attributi seguenti:

* **Nome**: nome assegnato al servizio Gestione dispositivi StorSimple al momento della creazione. **Il nome non può essere modificato dopo la creazione del servizio. Questo vale anche per altre entità, ad esempio dispositivi, volumi, contenitori dei volumi e criteri di backup che non è possibile rinominare nel portale di Azure.**
* **Stato**: stato del servizio, che può essere **Attivo**, **Creazione in corso** oppure **Online**.
* **Posizione** : posizione geografica in cui verrà distribuito il dispositivo StorSimple.
* **Sottoscrizione** : sottoscrizione di fatturazione associata al servizio.

## <a name="move-a-service-to-azure-portal"></a>Spostare un servizio nel portale di Azure
Ora è possibile gestire i dispositivi StorSimple serie 8000 nel portale di Azure. Se si dispone di un servizio esistente per gestire i dispositivi StorSimple, è consigliabile spostare il servizio nel portale di Azure. Il portale di Azure classico per il servizio StorSimple Manager non è supportato dopo il 30 settembre 2017. Se si vuole passare al nuovo portale di Azure, fare riferimento a [Considerazioni per la transizione](#considerations-for-transition). 

> [!NOTE]
> A partire dal 5 ottobre 2017, le istanze classiche di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure. Si tratta di un'implementazione graduale e gli utenti saranno aggiornati sugli spostamenti tramite notifiche via e-mail e del portale. Per eventuali domande, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

### <a name="considerations-for-transition"></a>Considerazioni per la transizione

Esaminare l'impatto della migrazione al nuovo portale di Azure prima di spostare il servizio.

> [!NOTE]
> I cmdlet PowerShell esistenti di gestione del servizio Azure non sono supportati dopo lo spostamento al nuovo portale di Azure. Aggiornare gli script per gestire i dispositivi tramite Azure Resource Manager SDK. Per altre informazioni, vedere [Usare script basati su Azure Resource Manager SDK per gestire dispositivi StorSimple](storsimple-8000-automation-azurerm-scripts.md).
> Il nuovo portale di Azure supporta i dispositivi che eseguono l'aggiornamento 3.0 o versione successiva. Se il dispositivo non è aggiornato, è consigliabile applicare l'aggiornamento 5 appena possibile.

#### <a name="before-you-transition"></a>Prima di eseguire la transizione

* Nel dispositivo è installato l'aggiornamento 3.0 o versione successiva. Se il dispositivo esegue una versione precedente, è consigliabile installare l'aggiornamento 5 tramite il metodo hotfix. Per altre informazioni, vedere [Installare l'aggiornamento 5](storsimple-8000-install-update-5.md). Se si usa un'appliance cloud StorSimple (8010/8020), non è possibile aggiornare un'appliance cloud. Usare la versione più recente del software per creare una nuova appliance cloud con l'aggiornamento 5.0 e quindi effettuare il failover nella nuova appliance cloud creata.

* Dopo aver eseguito la transizione al nuovo portale di Azure, non è possibile usare il portale di Azure classico per gestire il dispositivo StorSimple.

* La transizione non comporta interruzioni e tempi di inattività per il dispositivo.

* Viene eseguita la transizione di tutte le istanze di Gestione dispositivi StorSimple per la sottoscrizione specificata.

#### <a name="during-the-transition"></a>Durante la transizione

* Non è possibile gestire il dispositivo dal portale.
* Operazioni quali la suddivisione in livelli e i backup pianificati continuano a essere eseguite.
* Non eliminare le istanze di Gestione dispositivi StorSimple precedenti mentre la transizione è in corso.

#### <a name="after-the-transition"></a>Dopo la transizione

* Non è più possibile gestire i dispositivi dal portale classico.

* I cmdlet PowerShell esistenti di gestione dei servizi di Azure non sono supportati. Aggiornare gli script per gestire i dispositivi tramite Azure Resource Manager. Per script di esempio basati su Resource Manager SDK, fare riferimento a [storsimpledevicemgmttools github](https://github.com/anoobbacker/storsimpledevicemgmttools).

* La configurazione di servizi e dispositivi viene mantenuta. Viene anche eseguita la transizione al portale di Azure di tutti i volumi e i backup.

### <a name="begin-transition"></a>Iniziare la transizione

Eseguire la procedura seguente per effettuare la transizione del servizio al portale di Azure.

1. Passare al servizio StorSimple Manager esistente nel nuovo portale di Azure.
    ![Altri servizi](./media/storsimple-8000-manage-service/service-browse01.png) ![Selezionare Gestione dispositivi](./media/storsimple-8000-manage-service/service-browse02.png)

2. Viene visualizzata una notifica che segnala che il servizio Gestione dispositivi StorSimple è ora disponibile nel portale di Azure. Nel portale di Azure il servizio è denominato Gestione dispositivi StorSimple.
    ![Notifica della migrazione](./media/storsimple-8000-manage-service/service-transition1.jpg)
    
    1. Assicurarsi di aver esaminato l'impatto complessivo della migrazione.
    2. Esaminare l'elenco delle istanze di Gestione dispositivi StorSimple che verranno spostate dal portale classico.

3. Fare clic su **Esegui la migrazione**. Verrà avviato il processo di transizione, il cui completamento richiede alcuni minuti.

Al termine della transizione, è possibile gestire i dispositivi tramite il servizio Gestione dispositivi StorSimple nel portale di Azure. Se non viene visualizzata l'opzione per la migrazione al portale di Azure ma si vuole comunque passare al portale, è possibile [inviare una richiesta](https://aka.ms/ss8000-cx-signup).

## <a name="supported-operations-on-devices-running-versions-prior-to-update-30"></a>Operazioni supportate nei dispositivi che eseguono una versione precedente all'aggiornamento 3.0
Nel portale di Azure sono supportati solo i dispositivi StorSimple che eseguono l'aggiornamento 3.0 e versioni successive. I dispositivi che eseguono versioni precedenti hanno un supporto limitato. Dopo avere eseguito la migrazione al portale di Azure, usare la tabella seguente per comprendere le operazioni supportate per i dispositivi con versioni precedenti all'aggiornamento 3.0.

| Operazione                                                                                                                       | Supportato      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrare un dispositivo                                                                                                               | Sì            |
| Configurare le impostazioni di dispositivo, ad esempio le caratteristiche generali, la rete e la sicurezza                                                                | Sì            |
| Analizzare, scaricare e installare aggiornamenti                                                                                             | Sì            |
| Disattivare un dispositivo                                                                                                               | Sì            |
| Eliminare un dispositivo                                                                                                                   | Sì            |
| Creare, modificare ed eliminare un contenitore di volumi                                                                                   | No             |
| Creare, modificare ed eliminare un volume                                                                                             | No             |
| Creare, modificare ed eliminare criteri di backup                                                                                      | No             |
| Creazione di un backup manuale                                                                                                            | No             |
| Eseguire un backup pianificato                                                                                                         | Non applicabile |
| Eseguire il ripristino da un set di backup                                                                                                        | No             |
| Eseguire la clonazione in un dispositivo che esegue l'aggiornamento 3.0 e versioni successive <br> Il dispositivo di origine esegue una versione precedente all'aggiornamento 3.0.                                | Sì            |
| Eseguire la clonazione in un dispositivo che esegue una versione precedente all'aggiornamento 3.0.                                                                          | No             |
| Eseguire il failover del dispositivo di origine <br> (da un dispositivo con una versione precedente all'aggiornamento 3.0 a un dispositivo con l'aggiornamento 3.0 e versioni successive)                                                               | Sì            |
| Eseguire il failover del dispositivo di destinazione <br> (a un dispositivo che esegue una versione software precedente all'aggiornamento 3.0)                                                                                   | No             |
| Cancellare un avviso                                                                                                                  | Sì            |
| Visualizzare criteri di backup, catalogo di backup, volumi, contenitori di volumi, grafici di monitoraggio, processi e avvisi creati nel portale classico | Sì            |
| Attivare e disattivare i controller dei dispositivi                                                                                              | Sì            |


## <a name="delete-a-service"></a>Eliminare un servizio

Prima di eliminare un servizio, verificare che non sia usato da dispositivi connessi. Se il servizio è in uso, disattivare i dispositivi connessi. L'operazione di disattivazione interromperà la connessione tra il dispositivo e il servizio mantenendo i dati del dispositivo nel cloud.

> [!IMPORTANT]
> Dopo che un servizio è stato eliminato, l'operazione non può essere annullata. Sarà necessario ripristinare le impostazioni predefinite di ogni dispositivo che usava il servizio prima che il dispositivo possa essere usato con un altro servizio. In questo scenario, i dati locali sul dispositivo e la configurazione vanno persi.

Per eliminare un servizio, attenersi alla procedura seguente.

### <a name="to-delete-a-service"></a>Per eliminare un servizio

1. Cercare il servizio da eliminare. Fare clic sull'icona **Risorse** e quindi immettere i termini appropriati per la ricerca. Nei risultati della ricerca selezionare il servizio da eliminare.

    ![Cercare il servizio da eliminare](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Verrà visualizzato il pannello del servizio Gestione dispositivi StorSimple. Fare clic su **Elimina**.

    ![Delete service](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Nel messaggio di richiesta di conferma fare clic su **Sì** . L'eliminazione del servizio può richiedere alcuni minuti.

    ![Confermare l'eliminazione](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio

Dopo aver creato un servizio, è necessario registrare il dispositivo StorSimple. Per registrare il primo dispositivo StorSimple, è necessaria la chiave di registrazione del servizio. Per registrare altri dispositivi con un servizio StorSimple esistente, sono necessarie la chiave di registrazione e la chiave DEK del servizio (che viene generata durante la registrazione sul primo dispositivo). Per altre informazioni sulla chiave DEK del servizio, vedere [Sicurezza in StorSimple](storsimple-8000-security.md). È possibile ottenere la chiave di registrazione accedendo a **Chiavi** nel pannello Gestione dispositivi StorSimple.

Per ottenere la chiave di registrazione del servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Conservare la chiave di registrazione del servizio in una posizione sicura. Questa chiave e la chiave DEK del servizio saranno necessarie per registrare altri dispositivi con il servizio. Dopo aver ottenuto la chiave di registrazione del servizio, è necessario configurare il dispositivo tramite l'interfaccia di Windows PowerShell per StorSimple.

Per informazioni dettagliate sull'uso della chiave di registrazione, vedere [Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio
La rigenerazione di una chiave di registrazione del servizio deve essere effettuata quando è necessario eseguire la rotazione delle chiavi o se l'elenco di amministratori del servizio è stato modificato. Quando si rigenera la chiave, quest'ultima viene usata solo per registrare dispositivi successivi. I dispositivi già registrati non sono interessati da questo processo.

Per rigenerare una chiave di registrazione del servizio, attenersi alla procedura seguente.

### <a name="to-regenerate-the-service-registration-key"></a>Per rigenerare la chiave di registrazione del servizio
1. In **Gestione dispositivi StorSimple** passare a **Gestione&gt;** **Chiavi**.
    
    ![Pannello Chiavi](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Nel pannello **Chiavi** fare clic su **Rigenera**.

    ![Fare clic su Rigenera](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Nel pannello **Rigenera la chiave di registrazione del servizio** esaminare l'azione necessaria quando le chiavi vengono rigenerate. Tutti i dispositivi successivi registrati con questo servizio useranno la nuova chiave di registrazione. Fare clic su **Rigenera** per confermare. Al termine della rigenerazione, si riceverà una notifica.

    ![Confermare la rigenerazione](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Verrà visualizzata una nuova chiave di registrazione del servizio.

5. Copiare la chiave e salvarla per registrare eventuali nuovi dispositivi con il servizio.



## <a name="change-the-service-data-encryption-key"></a>Modificare la chiave DEK del servizio
Le chiavi DEK del servizio vengono utilizzate per crittografare dati riservati dei clienti, ad esempio credenziali di account di archiviazione, che vengono inviate dal servizio StorSimple Manager al dispositivo StorSimple. Occorre modificare queste chiavi periodicamente, se l'organizzazione IT dispone di un criterio di rotazione delle chiavi nei dispositivi di archiviazione. Il processo di modifica della chiave può essere leggermente diverso a seconda che ci sia una o più dispositivi gestiti dal servizio StorSimple Manager. Per altre informazioni, vedere [Sicurezza e protezione dei dati di StorSimple](storsimple-8000-security.md).

La modifica della chiave DEK del servizio è un processo che prevede 3 fasi:

1. Usando gli script di Windows PowerShell per Azure Resource Manager, autorizzare un dispositivo a modificare la chiave DEK del servizio.
2. Mediante Windows PowerShell per StorSimple, si consente di avviare la modifica della chiave DEK del servizio.
3. Se si dispone di più di un dispositivo StorSimple, è necessario aggiornare la chiave DEK del servizio in altri dispositivi.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passaggio 1: Usare uno script di Windows PowerShell per autorizzare un dispositivo a modificare la chiave DEK del servizio
In genere, l'amministratore dei dispositivi richiede all'amministratore del servizio di autorizzare un dispositivo a modificare le chiavi DEK del servizio. L'amministratore del servizio autorizza quindi il dispositivo a modificare la chiave.

Questo passaggio viene eseguito usando lo script basato su Azure Resource Manager. L'amministratore del servizio può selezionare un dispositivo idoneo per l'autorizzazione. Il dispositivo viene quindi autorizzato ad avviare il processo di modifica della chiave DEK del servizio. 

Per altre informazioni sull'uso dello script, vedere [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quali dispositivi possono essere autorizzati a modificare le chiavi DEK del servizio?
Per essere autorizzato ad avviare le modifiche alla chiave DEK del servizio, un dispositivo deve soddisfare i criteri seguenti:

* Il dispositivo deve essere online per essere idoneo per l'autorizzazione di modifica della chiave DEK del servizio.
* È possibile autorizzare di nuovo lo stesso dispositivo dopo 30 minuti se la modifica della chiave non è stata avviata.
* È possibile autorizzare un dispositivo diverso, purché la modifica della chiave non sia stata avviata dal dispositivo autorizzato in precedenza. Dopo che il nuovo dispositivo è stato autorizzato, il dispositivo precedente non può avviare la modifica.
* Non è possibile autorizzare un dispositivo mentre è in corso il rollover della chiave DEK del servizio.
* È possibile autorizzare un dispositivo quando alcuni dei dispositivi registrati con il servizio hanno eseguito il rollover della crittografia mentre altri no. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio
Questo passaggio viene eseguito nell'interfaccia di Windows PowerShell per StorSimple nel dispositivo StorSimple autorizzato.

> [!NOTE]
> Non è possibile eseguire operazioni nel portale di Azure del servizio StorSimple Manager fino a quando il rollover della chiave non viene completato.
> 
> 

Se si usa la console seriale del dispositivo per la connessione all'interfaccia di Windows PowerShell,seguire questa procedura.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Per avviare la modifica della chiave DEK del servizio
1. Selezionare l'opzione 1 per eseguire l'accesso completo.
2. Al prompt dei comandi digitare:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Dopo che il cmdlet è stato completato, si otterrà una nuova chiave DEK del servizio. Copiare e salvare la chiave per l'uso nel passaggio 3 di questo processo. Questa chiave verrà usata per aggiornare tutti i dispositivi rimanenti registrati con il servizio StorSimple Manager.
   
   > [!NOTE]
   > Questo processo deve essere avviato entro quattro ore dall'autorizzazione di un dispositivo StorSimple.
   > 
   > 
   
   La nuova chiave viene quindi inviata al servizio affinché ne venga effettuato il push a tutti i dispositivi registrati con il servizio. Nel dashboard del servizio verrà visualizzato un avviso. Il servizio disabiliterà tutte le operazioni nei dispositivi registrati e l'amministratore dei dispositivi dovrà quindi aggiornare la chiave DEK del servizio negli altri dispositivi. Tuttavia, i flussi di I/O (invio di dati dagli host al cloud) non verranno interrotti.
   
   Se nel servizio è registrato un unico dispositivo, il processo di rollover è completo ed è possibile ignorare il passaggio successivo. Se nel servizio sono registrati più dispositivi, andare al passaggio 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passaggio 3: Aggiornare la chiave DEK del servizio in altri dispositivi StorSimple
Questa procedura deve essere eseguita nell'interfaccia di Windows PowerShell del dispositivo StorSimple se vi sono più dispositivi registrati per il servizio StorSimple Manager. La chiave ottenuta nel passaggio 2 deve essere usata per aggiornare tutti i dispositivi StorSimple rimanenti registrati con il servizio StorSimple Manager.

Eseguire i passaggi seguenti per aggiornare la chiave DEK del servizio nel dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Per aggiornare la chiave di crittografia dei dati del servizio nei dispositivi fisici
1. Usare Windows PowerShell per StorSimple per connettersi alla console. Selezionare l'opzione 1 per eseguire l'accesso completo.
2. Al prompt dei comandi digitare: `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Specificare la chiave DEK ottenuta nel [Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Per aggiornare la chiave di crittografia dei dati del servizio in tutte le appliance cloud 8010/8020
1. Scaricare e configurare lo script di PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1). 
2. Aprire PowerShell e al prompt dei comandi digitare: `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Questo script garantisce che la chiave di crittografia dei dati del servizio sia configurata in tutte le appliance cloud 8010/8020 nel servizio Gestione dispositivi.

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [processo di distribuzione di StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Ulteriori informazioni sulla [gestione dell'account di archiviazione di StorSimple](storsimple-8000-manage-storage-accounts.md).
* Altre informazioni su come [usare il servizio Gestione dispositivi StorSimple per amministrare un dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
