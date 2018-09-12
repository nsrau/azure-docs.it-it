---
title: Risolvere i problemi della replica da Hyper-V ad Azure con Azure Site Recovery | Microsoft Docs
description: Descrive come risolvere i problemi della replica da Hyper-V ad Azure usando Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rayne
ms.openlocfilehash: d5282e5954aa50ce67d6341b194177a89bdbe6cc
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666402"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Risolvere i problemi della replica e del failover da Hyper-V ad Azure

Questo articolo descrive i problemi comuni che possono verificarsi durante l'esecuzione della replica di macchine virtuali Hyper-V locali in Azure usando [Azure Site Recovery](site-recovery-overview.md).

## <a name="enable-protection-issues"></a>Problemi di abilitazione della protezione

Se si verificano problemi quando si abilita la protezione per le macchine virtuali Hyper-V, verificare gli elementi consigliati seguenti:

1. Controllare che gli host e le macchine virtuali Hyper-V siano conformi a tutti i [requisiti e prerequisiti](hyper-v-azure-support-matrix.md).
2. Se i server Hyper-V si trovano nei cloud di System Center Virtual Machine Manager (VMM), verificare che il [server VMM](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional) sia stato predisposto.
3. Verificare che il servizio Virtual Machine Management di Hyper-V sia in esecuzione negli host Hyper-V.
4. Controllare eventuali problemi che vengono visualizzati nell'accesso Hyper-V-VMMS\Admin alla macchina virtuale. Il log si trova in **Registri applicazioni e servizi** > **Microsoft** > **Windows**.
5. Nella macchina virtuale guest verificare che WMI sia abilitato e accessibile.
  - [Informazioni](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/) sul test WMI di base.
  - [Risolvere i problemi](https://aka.ms/WMiTshooting) di WMI.
  - [Risolvere i problemi ](https://technet.microsoft.com/library/ff406382.aspx#H22) con gli script e i servizi WMI.
6. Nella macchina virtuale guest verificare che sia in esecuzione la versione più recente di Integration Services.
    - [Controllare](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) che sia disponibile l'ultima versione.
    - [Mantenere](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) aggiornato Integration Services.
    
## <a name="replication-issues"></a>Problemi di replica

Risolvere i problemi relativi alla replica iniziale e in corso come indicato di seguito:

1. Assicurarsi di eseguire la [versione più recente](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) dei servizi di Site Recovery.
2. Verificare se la replica è stata sospesa:
  - Controllare lo stato di integrità della macchina virtuale nella console Hyper-V Manager.
  - Se è critico, fare clic con il pulsante destro del mouse sulla macchina virtuale > **Replica** > **Visualizza stato di replica**.
  - Se la replica è sospesa, fare clic su **Riprendi replica**.
3. Controllare che siano in esecuzione i servizi necessari. Se non lo sono, riavviarli.
    - Se si esegue la replica di Hyper-V senza VMM, verificare che questi servizi siano in esecuzione nell'host Hyper-V:
        - Servizio Virtual Machine Management
        - Servizio Agente di Servizi di ripristino di Microsoft Azure
        - Servizio Microsoft Azure Site Recovery
        - Servizio Host provider WMI
    - Se si esegue la replica con VMM nell'ambiente in uso, verificare che questi servizi siano in esecuzione:
        - Nell'host Hyper-V controllare che siano in esecuzione il servizio Virtual Machine Management, l'agente di Servizi di ripristino di Microsoft Azure e il servizio Host provider WMI.
        - Nel server VMM assicurarsi che sia in esecuzione il servizio System Center Virtual Machine Manager.
4. Verificare la connettività tra il server Hyper-V e Azure. Per controllare la connettività, aprire Gestione attività nell'host Hyper V. Nella scheda **Prestazioni** fare clic su **Apri Monitoraggio risorse**. Nella scheda **Rete** > **Process with Network Activity** (Processo con attività rete) controllare se cbengine.exe sta inviando attivamente volumi di dati di grandi dimensioni (MB).
5. Verificare se gli host Hyper-V sono in grado di connettersi all'URL del BLOB di archiviazione di Azure. Per verificare se gli host possono connettersi, selezionare e controllare **cbengine.exe**. Visualizzare **Connessioni TCP** per verificare la connettività dall'host al BLOB di archiviazione di Azure.
6. Verificare i problemi di prestazioni, come descritto di seguito.
    
### <a name="performance-issues"></a>Problemi di prestazioni

Le limitazioni della larghezza di banda di rete possono influire sulla replica. Risolvere i problemi nel modo seguente:

1. [Controllare](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) se sono presenti vincoli di limitazione delle richieste o di larghezza di banda nell'ambiente in uso.
2. Eseguire il [profiler Deployment Planner](hyper-v-deployment-planner-run.md).
3. Dopo avere eseguito il profiler, seguire le raccomandazioni per la [larghezza di banda](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e l'[archiviazione](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation).
4. Verificare le [limitazioni di varianza dati](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits). Se si riscontra una varianza dati elevata in una macchina virtuale, eseguire le operazioni seguenti:
  - Controllare se la macchina virtuale è contrassegnata per la risincronizzazione.
  - Seguire [questi passaggi](https://blogs.technet.microsoft.com/virtualization/2014/02/02/hyper-v-replica-debugging-why-are-very-large-log-files-generated/) per individuare l'origine della varianza.
  - La varianza può verificarsi quando i file di log HRL superano il 50% dello spazio disponibile su disco. Se questo è il problema, eseguire il provisioning di ulteriore spazio di archiviazione per tutte le macchine virtuali in cui si verifica il problema.
  - Verificare che la replica non sia sospesa. Se la replica è in pausa, continua a scrivere le modifiche nel file hrl, contribuendo ad aumentarne le dimensioni.
 

## <a name="critical-replication-state-issues"></a>Problemi di stato della replica critico

1. Per verificare l'integrità della replica, connettersi alla console Hyper-V Manager locale, selezionare la macchina virtuale e controllare l'integrità.

    ![Integrità della replica](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. Fare clic su **Visualizza stato di replica** per visualizzare i dettagli:

    - Se la replica è sospesa, fare clic con il pulsante destro del mouse sulla macchina virtuale > **Replica** > **Riprendi replica**.
    - Se viene eseguita la migrazione di una macchina virtuale su un host Hyper-V configurato in Site Recovery a un altro host Hyper-V nello stesso cluster o a un computer autonomo, la replica per la macchina virtuale non sarà interessata. Verificare semplicemente che il nuovo host Hyper-V soddisfi tutti i prerequisiti e sia configurato in Site Recovery.

## <a name="app-consistent-snapshot-issues"></a>Problemi di snapshot coerenti con l'app

Uno snapshot coerente con l'app è uno snapshot temporizzato dei dati dell'applicazione all'interno della macchina virtuale. Il servizio Copia Shadow del volume (VSS) assicura che lo stato delle app nella macchina virtuale sia coerente quando viene creato lo snapshot.  Questa sezione descrive in dettaglio alcuni problemi comuni che si potrebbero verificare.

### <a name="vss-failing-inside-the-vm"></a>Errore del servizio Copia Shadow del volume nella macchina virtuale

1. Verificare che sia installata e in esecuzione la versione più recente di Integration Services.  Controllare se è disponibile un aggiornamento eseguendo il comando seguente da un prompt di PowerShell con privilegi elevati nell'host Hyper-V: **get-vm | select Name, State, IntegrationServicesState**.
2. Verificare che il servizio Copia Shadow del volume sia in esecuzione e integro:
    - Per controllare i servizi, accedere alla macchina virtuale guest. Aprire quindi un prompt dei comandi come amministratore ed eseguire i comandi seguenti per verificare che tutti i VSS writer siano integri.
        - **Vssadmin list writers**
        - **Vssadmin list shadows**
        - **Vssadmin list providers**
    - Controllare l'output. Se i writer sono in uno stato di errore, eseguire le operazioni seguenti:
        - Controllare il log eventi dell'applicazione nella macchina virtuale per individuare eventuali errori dell'operazione del servizio Copia Shadow del volume.
    - Provare a riavviare questi servizi associati con il writer con errori:
        - Copia Shadow del volume
         - Provider VSS di Azure Site Recovery
    - Al termine dell'operazione, attendere un paio d'ore per verificare se gli snapshot coerenti con l'app vengono generati correttamente.
    - Come ultima risorsa provare a riavviare la macchina virtuale. Questa operazione potrebbe risolvere i servizi che si trovano in uno stato di mancata risposta.
3. Verificare di non avere dischi dinamici nella macchina virtuale. Questa funzionalità non è supportata per gli snapshot coerenti con l'app. È possibile controllare in Gestione disco (diskmgmt.msc).

    ![Disco dinamico](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. Controllare che non ci sia un disco iSCSI collegato alla macchina virtuale. Questa funzionalità non è supportata.
5. Controllare che il servizio Backup sia abilitato. Verificare l'abilitazione in **Impostazioni Hyper-V** > **Integration Services**.
6. Assicurarsi che non ci siano conflitti con le app che creano snapshot VSS. I conflitti possono verificarsi se più app tentano di creare snapshot VSS contemporaneamente, ad esempio se un'app di Backup crea snapshot VSS quando Site Recovery è pianificato dai criteri di replica per creare uno snapshot.   
7. Controllare se nella macchina virtuale viene riscontrata una varianza elevata:
    - È possibile misurare la frequenza di modifica dei dati giornaliera per le macchine virtuali guest, usando i contatori delle prestazioni nell'host Hyper-V. Per misurare la frequenza di modifica dei dati abilitare il contatore seguente. Aggregare un campione di questo valore tra i dischi delle macchine virtuali per 5-15 minuti, in modo da ottenere la varianza della macchina virtuale.
        - Categoria: “Dispositivo di archiviazione virtuale Hyper-V”
        - Contatore: “Byte scritti/sec”</br>
        - Questa varianza dei dati aumenterà o rimarrà a un livello elevato, a seconda del carico di lavoro della macchina virtuale o delle relative app.
        - La varianza media dei dati del disco di origine è 2 MB/s per l'archiviazione standard per Site Recovery. [Altre informazioni](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - È anche possibile [verificare gli obiettivi di scalabilità delle risorse di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#scalability-targets-for-a-storage-account).
8. Eseguire [Deployment Planner](hyper-v-deployment-planner-run.md).
9. Esaminare le raccomandazioni per la [rete](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input) e l'[archiviazione](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input).


### <a name="vss-failing-inside-the-hyper-v-host"></a>Errore del servizio Copia Shadow del volume nell'host Hyper-V

1. Controllare i log eventi per individuare eventuali errori e raccomandazioni per il servizio Copia Shadow del volume:
    - Nel server host Hyper-V aprire il log eventi di amministrazione di Hyper-V in **Visualizzatore eventi** > **Registri applicazioni e servizi** > **Microsoft** > **Windows** > **Hyper-V** > **Admin**.
    - Verificare se sono presenti eventi che indicano errori di snapshot coerenti con l'app.
    - Un errore tipico è analogo al seguente: "Non è stato possibile generare il set di snapshot VSS per la macchina virtuale 'XYZ'. Errore non temporaneo nel processo di scrittura. Il riavvio del servizio Copia Shadow del volume potrebbe risolvere il problema se il servizio non risponde".

2. Per generare gli snapshot VSS per la macchina virtuale, verificare che nella macchina virtuale sia installato Integration Services di Hyper-V e che il servizio di integrazione Backup (VSS) sia abilitato.
    - Controllare che il servizio/daemon VSS di Integration Services siano in esecuzione nel guest e si trovino in uno stato **OK**.
    - È possibile effettuare questa verifica da una sessione di PowerShell con privilegi elevati nell'host Hyper-V con il comando **et-VMIntegrationService -VMName<VMName>-Name VSS**. È anche possibile ottenere queste informazioni eseguendo l'accesso alla macchina virtuale guest. [Altre informazioni](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services).
    - Verificare che Backup/VSS Integration Services nella macchina virtuale siano in esecuzione e si trovino in uno stato integro. In caso contrario, riavviare questi servizi e il servizio richiedente Copia Shadow del volume di Hyper-V nel server host Hyper-V.

### <a name="common-errors"></a>Errori comuni

**Codice errore** | **Messaggio** | **Dettagli**
--- | --- | ---
**0x800700EA** | "Non è stato possibile generare il set di snapshot VSS per la macchina virtuale. Sono disponibili più dati (0x800700EA). Potrebbe non essere possibile generare il set di snapshot VSS se l'operazione di backup è in corso.<br/><br/> Operazione di replica per la macchina virtuale non riuscita. Sono disponibili più dati". | Verificare se la macchina virtuale dispone di un disco dinamico abilitato. Questa funzionalità non è supportata.
**0x80070032** | "Il richiedente del servizio Copia Shadow del volume di Hyper-V non è riuscito a connettersi alla macchina virtuale <./NomeVM> perché la versione non corrisponde alla versione prevista da Hyper-V". | Verificare se sono installati gli ultimi aggiornamenti di Windows.<br/><br/> [Eseguire l'aggiornamento](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date) alla versione più recente di Integration Services.



## <a name="collect-replication-logs"></a>Raccogliere i log delle repliche

Tutti gli eventi di replica di Hyper-V vengono registrati nel log Hyper-V-VMMS\Admin, disponibile in **Registri applicazioni e servizi** > **Microsoft** > **Windows**. È anche possibile abilitare un registro analitico per il servizio Virtual Machine Management di Hyper-V, come indicato di seguito:

1. Rendere i registri analitici e di debug visibili nel Visualizzatore eventi. Per rendere disponibili i log nel Visualizzatore eventi fare clic su **Visualizza** > **Show Analytic and Debug Logs** (Visualizza log analitici e di debug). Il registro analitico viene visualizzato in **Hyper-V-VMMS**.
2. Nel riquadro **Azioni** fare clic su **Attiva registro**. 

    ![Attiva registro](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. Dopo l'attivazione viene visualizzato in **Performance Monitor**, come **sessione di traccia eventi** in **Insiemi agenti di raccolta dati**. 
4. Per visualizzare le informazioni raccolte, interrompere la sessione di traccia disabilitando il registro. Quindi salvare il registro e aprirlo nuovamente nel Visualizzatore eventi o usare altri strumenti per convertirlo come richiesto.


### <a name="event-log-locations"></a>Percorsi dei log eventi

**Log eventi** | **Dettagli** |
--- | ---
**Registri applicazioni e servizi/Microsoft/VirtualMachineManager/Server/Admin** (server VMM) | Log per la risoluzione dei problemi di VMM.
**Registri applicazioni e servizi/MicrosoftAzureRecoveryServices/Replication** (host Hyper-V) | Log per la risoluzione dei problemi dell'agente di Servizi di ripristino di Microsoft Azure. 
**Registri applicazioni e servizi/Microsoft/Azure Site Recovery/Provider/Operational** (host Hyper-V)| Log per la risoluzione dei problemi del servizio Microsoft Azure Site Recovery.
**Registri applicazioni e servizi/Microsoft/Windows/Hyper-V-VMMS/Admin** (host Hyper-V) | Log per la risoluzione dei problemi di gestione delle macchine virtuali Hyper-V.

### <a name="log-collection-for-advanced-troubleshooting"></a>Raccolta dei log per la risoluzione avanzata dei problemi

Questi strumenti possono essere utili per la risoluzione avanzata dei problemi:

-   Per VMM, eseguire la raccolta dei log di Site Recovery tramite lo [strumento SDP (Support Diagnostics Platform)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx).
-   Per Hyper-V senza VMM, [scaricare questo strumento](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) ed eseguirlo nell'host Hyper-V per raccogliere i log.

