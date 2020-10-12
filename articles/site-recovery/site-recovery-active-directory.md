---
title: Configurare il ripristino di emergenza Active Directory/DNS con Azure Site Recovery
description: Questo articolo descrive come implementare una soluzione di ripristino di emergenza per Active Directory e DNS con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mayg
ms.openlocfilehash: 528a24bb64aa8d323b5d63a27af0a52ccdf1abb6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132324"
---
# <a name="set-up-disaster-recovery-for-active-directory-and-dns"></a>Configurare il ripristino di emergenza per Active Directory e DNS

Le applicazioni aziendali, ad esempio SharePoint, Dynamics AX e SAP, dipendono dall'infrastruttura di Active Directory e DNS per funzionare correttamente. Quando si configura il ripristino di emergenza per le applicazioni, spesso è necessario ripristinare Active Directory e Domain Name System (DNS) prima di ripristinare altri componenti dell'applicazione, per garantire la corretta funzionalità dell'applicazione.

È possibile usare [Site Recovery](site-recovery-overview.md) per creare un piano di ripristino di emergenza per Active Directory. Quando si verifica un'interruzione, è possibile avviare un failover. Il servizio Active Directory può diventare operativo in pochi minuti. Se è stato distribuito Active Directory per più applicazioni nel sito principale, ad esempio per SharePoint e SAP, si potrebbe voler eseguire il failover del sito completo. È possibile eseguire in primis il failover di Active Directory attraverso Site Recovery. Quindi eseguire il failover delle altre applicazioni usando i piani di ripristino specifici delle applicazioni.

Questo articolo spiega come creare una soluzione di ripristino di emergenza per Active Directory. Include i prerequisiti e le istruzioni di failover. È necessario conoscere Active Directory e Site Recovery prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Se si esegue la replica in Azure, [preparare le risorse di Azure](tutorial-prepare-azure.md), inclusi una sottoscrizione, una rete virtuale di Azure, un account di archiviazione e un insieme di credenziali di Servizi di ripristino.
- Verificare i [requisiti di supporto](./vmware-physical-azure-support-matrix.md) per tutti i componenti.

## <a name="replicate-the-domain-controller"></a>Replicare il controller di dominio

- È necessario configurare Site Recovery replica in almeno una macchina virtuale (VM) che ospita un controller di dominio o DNS.
- Se si dispone di più controller di dominio nell'ambiente in uso, è necessario inoltre configurare un controller di dominio aggiuntivo nel sito di destinazione. Il controller di dominio aggiuntivo può essere in Azure o in un data center secondario locale.
- Se si hanno soltanto poche applicazioni e un controller di dominio, si potrebbe voler eseguire contestualmente il failover dell'intero sito. In questo caso, è consigliabile usare Site Recovery per replicare il controller di dominio nel sito di destinazione, in Azure o in un data center secondario locale. È possibile usare la stessa macchina virtuale controller di dominio o DNS replicata anche per il [failover di test](#test-failover-considerations).
- Se l'ambiente presenta molte applicazioni e più controller di dominio o se si intende eseguire il failover di poche applicazioni alla volta, oltre a replicare la macchina virtuale controller di dominio con Site Recovery si consiglia anche di configurare un controller di dominio aggiuntivo nel sito di destinazione (in Azure o in un data center locale secondario). Per il [failover di test](#test-failover-considerations), è possibile utilizzare un controller di dominio replicato da Site Recovery. Per il failover è possibile usare il controller di dominio aggiuntivo nel sito di destinazione.

## <a name="enable-protection-with-site-recovery"></a>Abilitare la protezione con Site Recovery

È possibile usare Site Recovery per proteggere la macchina virtuale che ospita il controller di dominio o DNS.

### <a name="protect-the-vm"></a>Proteggere la VM

Il controller di dominio replicato con Site Recovery viene usato per il [failover di test](#test-failover-considerations). Assicurarsi che soddisfi i requisiti seguenti:

1. Il controller di dominio è un server di catalogo globale.
1. Il controller di dominio deve essere il proprietario del ruolo FSMO (Flexible Single Master Operations) per i ruoli necessari durante un failover di test. In caso contrario questi ruoli dovranno essere [riassegnati](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) dopo il failover.

### <a name="configure-vm-network-settings"></a>Configurare le impostazioni di rete della VM

Per la macchina virtuale che ospita il controller di dominio o DNS, in Site Recovery configurare le impostazioni di rete nelle impostazioni **Calcolo e rete** della macchina virtuale replicata. In questo modo si garantisce che la macchina virtuale sia collegata alla rete corretta dopo il failover.

## <a name="protect-active-directory"></a>Proteggere Active Directory

### <a name="site-to-site-protection"></a>Protezione da sito a sito

Creare un controller di dominio nel sito secondario. Quando si innalza di livello il server a un ruolo di controller di dominio, specificare il nome dello stesso dominio usato nel sito primario. È possibile usare lo snap-in **Siti e servizi di Active Directory** per configurare le impostazioni nell'oggetto collegamento di sito a cui i siti vengono aggiunti. Configurando le impostazioni in un collegamento di sito, è possibile controllare quando viene eseguita la replica tra due o più siti e con quale frequenza. Per altre informazioni, vedere [Pianificazione della replica tra siti](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731862(v=ws.11)).

### <a name="site-to-azure-protection"></a>Protezione da sito ad Azure

Creare innanzitutto un controller di dominio in una rete virtuale di Azure. Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

Riconfigurare il server DNS per la rete virtuale per usare il server DNS in Azure.

:::image type="content" source="./media/site-recovery-active-directory/azure-network.png" alt-text="Rete di Azure":::

### <a name="azure-to-azure-protection"></a>Protezione da Azure a Azure

Creare innanzitutto un controller di dominio in una rete virtuale di Azure. Quando si alza di livello il server al ruolo di controller di dominio, specificare lo stesso nome di dominio usato nel sito primario.

Riconfigurare il server DNS per la rete virtuale per usare il server DNS in Azure.

## <a name="test-failover-considerations"></a>considerazioni sul failover di test

Per evitare effetti sui carichi di lavoro di produzione, il failover di test viene eseguito in una rete isolata dalla rete di produzione.

Molte applicazioni richiedono la presenza di un controller di dominio e di un server DNS. Perciò, prima del failover di un'applicazione, è necessario creare un controller di dominio nella rete isolata da usare per il failover di test. Il modo più semplice per eseguire questa operazione è usare Site Recovery per replicare una macchina virtuale che ospita un controller di dominio o DNS. Eseguire quindi un failover di test della macchina virtuale controller di dominio prima di eseguire un failover di test del piano di ripristino per l'applicazione.

1. Usare Site Recovery per [replicare](vmware-azure-tutorial.md) la macchina virtuale che ospita il controller di dominio o DNS.
1. Creare una rete isolata. Qualsiasi rete virtuale creata in Azure è isolata dalle altre reti per impostazione predefinita. Si consiglia di usare per questa rete lo stesso intervallo di indirizzi IP della rete di produzione. Non abilitare la connettività da sito a sito in questa rete.
1. Fornire un indirizzo IP DNS nella rete isolata. Usare l'indirizzo IP che si prevede sarà ottenuto dalla macchina virtuale DNS. Se si esegue la replica in Azure, fornire l'indirizzo IP per la macchina virtuale che viene usata in caso di failover. Per immettere l'indirizzo IP, nella macchina virtuale replicata, nelle impostazioni **Calcolo e rete**, selezionare le impostazioni **IP di destinazione**.

   :::image type="content" source="./media/site-recovery-active-directory/azure-test-network.png" alt-text="Rete di Azure":::

   > [!TIP]
   > Site Recovery tenta di creare le macchine virtuali di test in una subnet con lo stesso nome e con lo stesso indirizzo IP specificato nelle impostazioni **Calcolo e rete** della macchina virtuale. Se nella rete virtuale di Azure specificata per il failover di test non è disponibile una subnet con lo stesso nome, la macchina virtuale di test verrà creata nella prima subnet in ordine alfabetico.
   >
   > Se l'indirizzo IP di destinazione fa parte della subnet selezionata, Site Recovery tenta di creare la macchina virtuale per il failover di test usando l'indirizzo IP di destinazione. Se l'indirizzo IP di destinazione non fa parte della subnet selezionata, la macchina virtuale del failover di test viene creata usando l'indirizzo IP disponibile successivo nella subnet selezionata.

### <a name="test-failover-to-a-secondary-site"></a>Failover di test in un sito secondario

1. Se si esegue la replica in un altro sito locale e si usa DHCP, [configurare DNS e DHCP per il failover di test](hyper-v-vmm-test-failover.md#prepare-dhcp).
1. Eseguire un failover di test della macchina virtuale controller di dominio eseguita nella rete isolata. Per eseguire il failover di test, usare il più recente punto di ripristino _coerente con l'applicazione_ disponibile della macchina virtuale controller di dominio.
1. Eseguire un failover di test per il piano di ripristino che contiene le macchine virtuali in cui l'applicazione viene eseguita.
1. Al termine del test eseguire la _pulizia del failover di test_ nella macchina virtuale controller di dominio. Questo passaggio consente di eliminare il controller di dominio creato per il failover di test.

### <a name="remove-references-to-other-domain-controllers"></a>Rimuovere riferimenti ad altri controller di dominio

Quando si avvia un failover di test, non includere tutti i controller di dominio nella rete di test. Per rimuovere i riferimenti ad altri controller di dominio presenti nell'ambiente di produzione, è necessario [riassegnare i ruoli FSMO](https://support.microsoft.com/help/255504/using-ntdsutil-exe-to-transfer-or-seize-fsmo-roles-to-a-domain-control) ed [eseguire la pulizia](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc816907(v=ws.10)) dei metadati per i controller di dominio mancanti.

### <a name="issues-caused-by-virtualization-safeguards"></a>Problemi causati dalle misure di sicurezza della virtualizzazione

> [!IMPORTANT]
> Alcune configurazioni descritte in questa sezione non sono le configurazioni di controller di dominio standard o predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato per Site Recovery da usare per il failover di test. Apportare queste modifiche solo a quel controller di dominio.

A partire da Windows Server 2012, [misure di sicurezza aggiuntive sono integrate in Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Queste misure di sicurezza consentono di proteggere i controller di dominio virtualizzati dai rollback di numero di sequenza di aggiornamento (USN) se la piattaforma hypervisor sottostante supporta **VM-generazione**. Azure supporta **VM-GenerationID**, perciò nei controller di dominio che eseguono Windows Server 2012 o una versione successiva in macchine virtuali di Azure sono presenti queste misure di sicurezza aggiuntive.

Quando **VM-GenerationID** viene reimpostato, viene reimpostato anche il valore **InvocationID** del database di Active Directory Domain Services. Inoltre, il pool di ID relativo (RID) viene eliminato e la `SYSVOL` cartella è contrassegnata come non autorevole. Per ulteriori informazioni, vedere [Introduzione alla virtualizzazione di Active Directory Domain Services](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e virtualizzazione in [modo sicuro file System distribuito replica (DFSR)](https://techcommunity.microsoft.com/t5/storage-at-microsoft/safely-virtualizing-dfsr/ba-p/424671).

Il failover in Azure potrebbe provocare la reimpostazione di **VM-GenerationID**. La reimpostazione di **VM-GenerationID** attiva misure di sicurezza aggiuntive quando la macchina virtuale controller di dominio viene avviata in Azure. Questo potrebbe causare un ritardo significativo nella possibilità di accedere alla macchina virtuale del controller di dominio.

Poiché questo controller di dominio viene usato solo in un failover di test, non sono necessarie misure di sicurezza della virtualizzazione. Per assicurarsi che il valore **generazione** della macchina virtuale del controller di dominio non cambi, è possibile modificare il valore di seguente `DWORD` in **4** nel controller di dominio locale:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start`

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomi delle misure di sicurezza della virtualizzazione

Se le misure di sicurezza della virtualizzazione vengono attivate dopo un failover di test, possono verificarsi uno o più dei seguenti sintomi:

- Il valore **generazione** cambia:

  :::image type="content" source="./media/site-recovery-active-directory/Event2170.png" alt-text="Rete di Azure":::

- Il valore **invocationID** cambia:

  :::image type="content" source="./media/site-recovery-active-directory/Event1109.png" alt-text="Rete di Azure":::

- `SYSVOL` la cartella e le `NETLOGON` condivisioni non sono disponibili.

  :::image type="content" source="./media/site-recovery-active-directory/sysvolshare.png" alt-text="Rete di Azure":::

  :::image type="content" source="./media/site-recovery-active-directory/Event13565.png" alt-text="Rete di Azure":::

- I database DFSR vengono eliminati.

  :::image type="content" source="./media/site-recovery-active-directory/Event2208.png" alt-text="Rete di Azure":::

### <a name="troubleshoot-domain-controller-issues-during-test-failover"></a>Risolvere i problemi del controller di dominio durante il failover di test

> [!IMPORTANT]
> Alcune configurazioni descritte in questa sezione non sono le configurazioni di controller di dominio standard o predefinite. Se non si vuole apportare queste modifiche a un controller di dominio in produzione, è possibile creare un controller di dominio dedicato per il failover di test di Site Recovery. Apportare le modifiche solo a quel controller di dominio dedicato.

1. Al prompt dei comandi eseguire il comando seguente per verificare se la `SYSVOL` cartella e la `NETLOGON` cartella sono condivise:

    `NET SHARE`

1. Al prompt dei comandi eseguire questo comando per verificare che il controller di dominio funzioni correttamente:

    `dcdiag /v > dcdiag.txt`

1. Nel log di output cercare il testo seguente. Il testo conferma che il controller di dominio funziona correttamente.

    - `passed test Connectivity`
    - `passed test Advertising`
    - `passed test MachineAccount`

Se le condizioni precedenti sono soddisfatte, è probabile che il controller di dominio stia funzionando correttamente. In caso contrario, completare i passaggi seguenti:

1. Eseguire un ripristino autorevole del controller di dominio. Tenere presenti le seguenti informazioni:

    - Sebbene non sia consigliabile eseguire la replica tramite il [servizio Replica file](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-end-is-nigh-for-frs-8211-updated-for-ws2016/ba-p/425379), se si utilizza la replica FRS, attenersi alla procedura per un ripristino autorevole. La procedura è descritta in [Using the BurFlags registry key to reinitialize File Replication Service](https://support.microsoft.com/kb/290762) (Uso della chiave del Registro di sistema BurFlags per reinizializzare il servizio Replica file).

      Per altre informazioni su BurFlags, vedere il post di blog [D2 and D4: What is it for?](/archive/blogs/janelewis/d2-and-d4-what-is-it-for) (D2 e D4: a cosa servono?).

    - Se si usa la replica DFSR, completare i passaggi per il ripristino autorevole. Il processo è descritto in [forzare una sincronizzazione autorevole e non autorevole per la cartella SYSVOL con replica DFSR (ad esempio "D4/D2" per FRS)](https://support.microsoft.com/kb/2218556).

      È anche possibile usare le funzioni di PowerShell. Per altre informazioni, vedere [DFSR-SYSVOL authoritative/non-authoritative restore PowerShell functions](/archive/blogs/thbouche/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions) (Funzioni di PowerShell per il ripristino autorevole/non autorevole di DFSR-SYSVOL).

1. Aggirare il requisito di sincronizzazione iniziale impostando la chiave del Registro di sistema seguente su **0** nel controller di dominio locale. Se `DWORD` non esiste, è possibile crearlo nel nodo **parametri** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations`

   Per altre informazioni, vedere [Troubleshoot DNS Event ID 4013: The DNS server was unable to load AD integrated DNS zones](https://support.microsoft.com/kb/2001093) (Risoluzione dei problemi per l'evento DNS ID 4013: il server DNS non è riuscito a caricare zone DNS integrate in AD).

1. Disabilitare il requisito della disponibilità di un server di catalogo globale per la convalida dell'accesso utente. A tale scopo, nel controller di dominio locale impostare la seguente chiave del Registro di sistema su **1**. Se `DWORD` non esiste, è possibile crearlo nel nodo **LSA** .

   `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures`

   Per ulteriori informazioni, vedere funzionamento [del catalogo globale](/previous-versions/windows/it-pro/windows-server-2003/cc737410(v=ws.10)).

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controller di dominio su computer diversi

Se si esegue il controller di dominio e il DNs nella stessa macchina virtuale, è possibile ignorare questa procedura.

Se DNS non è presente nella stessa macchina virtuale del controller di dominio, è necessario creare una VM DNS per il failover di test. È possibile usare un server DNS nuovo e creare tutte le zone necessarie. Ad esempio, se il dominio Active Directory è `contoso.com` , è possibile creare una zona DNS con il nome `contoso.com` . Le voci corrispondenti a Active Directory devono essere aggiornate in DNS, come segue:

1. Verificare che queste impostazioni siano state definite prima che venga avviata qualsiasi altra macchina virtuale del piano di ripristino:

   - La zona deve avere il nome radice della foresta.
   - La zona deve essere sottoposta a backup.
   - La zona deve essere abilitata per aggiornamenti protetti e non protetti.
   - Il sistema di risoluzione della macchina virtuale che ospita il controller di dominio deve puntare all'indirizzo IP della macchina virtuale DNS.

1. Eseguire il comando seguente nella VM che ospita il controller di dominio:

   `nltest /dsregdns`

1. Eseguire i comandi seguenti per aggiungere una zona nel server DNS, consentire gli aggiornamenti non sicuri e aggiungere una voce per la zona in DNS:

   ```cmd
   dnscmd /zoneadd contoso.com  /Primary

   dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1

   dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>

   dnscmd /config contoso.com /allowupdate 1
   ```

## <a name="next-steps"></a>Passaggi successivi

[Scopri di più](site-recovery-workload.md) sulla protezione dei carichi di lavoro aziendali con Azure Site Recovery.
