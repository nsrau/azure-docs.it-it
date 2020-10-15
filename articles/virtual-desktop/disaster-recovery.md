---
title: Configurare il piano di ripristino di emergenza per desktop virtuali Windows-Azure
description: Come configurare un piano di continuità aziendale e ripristino di emergenza per la distribuzione di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 968f82a143872fd282222539ab71a70db488a20d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935705"
---
# <a name="set-up-a-business-continuity-and-disaster-recovery-plan"></a>Configurare una continuità aziendale e un piano di ripristino di emergenza

Per garantire la sicurezza dei dati dell'organizzazione, potrebbe essere necessario adottare una strategia di continuità aziendale e ripristino di emergenza (BCDR). Una strategia BCDR valida mantiene le app e i carichi di lavoro in esecuzione durante le interruzioni di servizio pianificate e non pianificate o di Azure.

Desktop virtuale di Windows offre BCDR per il servizio desktop virtuale di Windows per mantenere i metadati dei clienti durante le interruzioni. Quando si verifica un'interruzione in un'area, i componenti dell'infrastruttura del servizio eseguiranno il failover nella posizione secondaria e continueranno a funzionare normalmente. È comunque possibile accedere ai metadati relativi ai servizi e gli utenti possono comunque connettersi agli host disponibili. Le connessioni degli utenti finali rimarranno online fino a quando l'ambiente tenant o gli host rimangono accessibili.

Per assicurarsi che gli utenti possano ancora connettersi durante un'interruzione dell'area, è necessario replicare le macchine virtuali (VM) in un percorso diverso. Durante le interruzioni, il sito primario esegue il failover sulle macchine virtuali replicate nella posizione secondaria. Gli utenti possono continuare ad accedere alle app dalla posizione secondaria senza interruzioni. Oltre alla replica della macchina virtuale, è necessario lasciare le identità utente accessibili nella posizione secondaria. Se si usano i contenitori di profili, sarà anche necessario replicarli. Infine, assicurarsi che le app aziendali basate sui dati nella posizione primaria possano eseguire il failover con il resto dei dati.

Per riepilogare, per evitare che gli utenti si connettano durante un'interruzione, è necessario eseguire le operazioni seguenti nell'ordine indicato:

- Replicare le macchine virtuali in una posizione secondaria.
- Se si usano i contenitori di profili, configurare la replica dei dati nella posizione secondaria.
- Assicurarsi che le identità utente impostate nella posizione primaria siano disponibili nella posizione secondaria.
- Verificare che tutte le applicazioni line-of-business basate sui dati nella posizione primaria vengano sottoposte a failover nella posizione secondaria.

## <a name="vm-replication"></a>Replica VM

Prima di tutto, è necessario replicare le macchine virtuali nella posizione secondaria. Le opzioni disponibili variano a seconda della configurazione delle VM:

- Con Azure Site Recovery è possibile configurare tutte le VM per i pool host personali e in pool. Con questo metodo, sarà necessario configurare un solo pool host e i gruppi di app e le aree di lavoro correlati.
- È possibile creare un nuovo pool host nell'area di failover mantenendo disattivate tutte le risorse nel percorso di failover. Per questo metodo, è necessario configurare nuovi gruppi di app e aree di lavoro nell'area di failover. È quindi possibile usare un piano di Azure Site Recovery per attivare i pool host.
- È possibile creare un pool di host popolato da VM compilate in entrambe le aree primarie e di failover mantenendo le macchine virtuali nell'area di failover disattivate. In questo caso, è sufficiente configurare un pool host e i gruppi di app e le aree di lavoro correlati. È possibile usare un piano di Azure Site Recovery per accendere i pool host con questo metodo.

Si consiglia di usare [Azure Site Recovery](../site-recovery/site-recovery-overview.md) per gestire le macchine virtuali di replica in altre località di Azure, come descritto nell' [architettura di ripristino di emergenza da Azure ad Azure](../site-recovery/azure-to-azure-architecture.md). È consigliabile usare Azure Site Recovery per i pool di host personali, perché Azure Site Recovery supporta [SKU basati su server e client](../site-recovery/azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

Se si usa Azure Site Recovery, non è necessario registrare manualmente queste macchine virtuali. L'agente desktop virtuale di Windows nella macchina virtuale secondaria userà automaticamente il token di sicurezza più recente per connettersi all'istanza del servizio più vicina. La macchina virtuale (host sessione) nella posizione secondaria diventerà automaticamente parte del pool host. L'utente finale dovrà riconnettersi durante il processo, ma a parte ciò non ci sono altre operazioni manuali.

Se sono presenti connessioni utente durante l'interruzione, prima che l'amministratore possa avviare il failover nell'area secondaria, è necessario terminare le connessioni utente nell'area corrente.

Per disconnettere gli utenti in un desktop virtuale Windows (classico), eseguire il cmdlet seguente:

```powershell
Invoke-RdsUserSessionLogoff
```

Per disconnettere gli utenti nella versione integrata di Azure di desktop virtuale di Windows, eseguire il cmdlet seguente:

```powershell
Remove-AzWvdUserSession
```

Dopo aver effettuato la disconnessione di tutti gli utenti nell'area primaria, è possibile eseguire il failover delle macchine virtuali nell'area primaria e consentire agli utenti di connettersi alle macchine virtuali nell'area secondaria. Per altre informazioni sul funzionamento di questo processo, vedere [eseguire la replica di macchine virtuali di Azure in un'altra area di Azure](../site-recovery/azure-to-azure-how-to-enable-replication.md).

## <a name="virtual-network"></a>Rete virtuale

Si consideri quindi la connettività di rete durante l'interruzione. È necessario assicurarsi di aver configurato una rete virtuale (VNET) nell'area secondaria. Se gli utenti devono accedere alle risorse locali, è necessario configurare questo VNET per accedervi. È possibile stabilire connessioni locali con una VPN, ExpressRoute o una rete WAN virtuale.

Si consiglia di usare Azure Site Recovery per configurare il VNET nell'area di failover perché conserva le impostazioni della rete primaria e non richiede il peering.

## <a name="user-identities"></a>Identità utente

Verificare quindi che il controller di dominio sia disponibile nella posizione secondaria. 

Esistono tre modi per rendere disponibile il controller di dominio:

   - Disporre di Dominio di Active Directory controller nella posizione secondaria
   - Usare un controller di Dominio di Active Directory locale
   - Replicare Dominio di Active Directory controller usando [Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)

## <a name="user-and-app-data"></a>Dati dell'utente e dell'app

Se si usano i contenitori di profili, il passaggio successivo consiste nell'impostare la replica dei dati nella posizione secondaria. Sono disponibili cinque opzioni per archiviare i profili FSLogix:

   - Spazi di archiviazione diretta (S2D)
   - Unità di rete (VM con unità aggiuntive)
   - File di Azure
   - Azure NetApp Files
   - Cache cloud per la replica

Per altre informazioni, vedere [Opzioni di archiviazione per i contenitori di profili FSLogix in desktop virtuale di Windows](store-fslogix-profile.md).

Se si sta configurando il ripristino di emergenza per i profili, sono disponibili le opzioni seguenti:

   - Configurare la replica nativa di Azure, ad esempio File di Azure la replica dell'account di archiviazione standard, la replica Azure NetApp Files o File di Azure sincronizzazione per i file server.
    
     >[!NOTE]
     >La replica NetApp è automatica dopo averla configurata per la prima volta. Con i piani di Azure Site Recovery, è possibile aggiungere pre-script e post-script per eseguire il failover delle risorse non VM replicare le risorse di archiviazione di Azure.

   - Configurare la cache cloud FSLogix per i dati dell'app e dell'utente.
   - Configurare il ripristino di emergenza per i dati delle app solo per garantire l'accesso ai dati cruciali per l'azienda in qualsiasi momento. Con questo metodo è possibile recuperare i dati utente al termine dell'interruzione.

Si esaminerà ora come configurare FSLogix per configurare il ripristino di emergenza per ogni opzione.

### <a name="fslogix-configuration"></a>Configurazione di FSLogix

Se si configurano le voci del registro di sistema per FSLogix, l'agente FSLogix può supportare più percorsi del profilo.

Per configurare le voci del registro di sistema:

1. Aprire l' **Editor del registro di sistema**.
2. Passare a **computer**  >  **HKEY_LOCAL_MACHINE**  >  **software**  >  **FSLogix**  >  **profiles**.
   
     > [!div class="mx-imgBorder"]
     > ![Screenshot della finestra profili nell'editor del registro di sistema. VHDLocation è selezionato.](media/regedit-profiles.png)

3. Fare clic con il pulsante destro del mouse su **VHDLocations** e scegliere **Modifica multistringhe**.

     > [!div class="mx-imgBorder"]
     > ![Screenshot della finestra Modifica multistringhe. I dati relativi ai valori elencano le località Centrual US e East US.](media/multi-string-edit.png)

4. Nel campo **dati valore** immettere i percorsi che si desidera utilizzare.
5. Al termine, fare clic su **OK**.

Se la prima posizione non è disponibile, l'agente FSLogix eseguirà automaticamente il failover al secondo e così via.

Si consiglia di configurare l'agente FSLogix con il percorso della posizione secondaria nell'area principale. Una volta arrestata la posizione primaria, l'agente FLogix eseguirà la replica come parte della macchina virtuale Azure Site Recovery la replica. Una volta pronte le VM replicate, l'agente tenterà automaticamente di eseguire il percorso dell'area secondaria.

Si immagini, ad esempio, che le macchine virtuali host della sessione primaria si trovino nell'area Stati Uniti centrali, ma il contenitore del profilo si trovi nell'area Stati Uniti centrali per motivi di prestazioni.

In questo caso, è necessario configurare l'agente FSLogix con un percorso di archiviazione negli Stati Uniti centrali. È necessario configurare le VM host della sessione per la replica negli Stati Uniti occidentali. Quando il percorso degli Stati Uniti centrali ha esito negativo, l'agente tenterà invece di creare un nuovo percorso per l'archiviazione negli Stati Uniti occidentali.

### <a name="s2d"></a>S2D

Poiché S2D gestisce la replica tra le aree internamente, non è necessario configurare manualmente il percorso secondario.

### <a name="network-drives-vm-with-extra-drives"></a>Unità di rete (VM con unità aggiuntive)

Se si replicano le macchine virtuali di archiviazione di rete usando Azure Site Recovery come le VM host della sessione, il ripristino mantiene lo stesso percorso, ovvero non è necessario riconfigurare FSlogix.

### <a name="azure-files"></a>File di Azure

File di Azure supporta la replica asincrona tra più aree che è possibile specificare quando si crea l'account di archiviazione. Se la natura asincrona di File di Azure copre già gli obiettivi del ripristino di emergenza, non è necessario eseguire una configurazione aggiuntiva.

Se è necessaria la replica sincrona per ridurre al minimo la perdita di dati, è consigliabile usare invece la cache cloud di FSLogix.

>[!NOTE]
>Questa sezione non copre il meccanismo di autenticazione del failover per File di Azure.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Per altre informazioni sui Azure NetApp Files, vedere [creare un peering di replica per Azure NetApp files](../azure-netapp-files/cross-region-replication-create-peering.md).

## <a name="app-dependencies"></a>Dipendenze dell'app

Infine, assicurarsi che tutte le app aziendali basate sui dati che si trovano nell'area primaria possano eseguire il failover nella posizione secondaria. Assicurarsi anche di configurare le impostazioni che le app devono usare nella nuova posizione. Ad esempio, se una delle app dipende dal back-end SQL, assicurarsi di replicare SQL nella posizione secondaria. È necessario configurare l'app in modo che usi il percorso secondario come parte del processo di failover o come configurazione predefinita. È possibile modellare le dipendenze dell'app nei piani Azure Site Recovery. Per altre informazioni, vedere [informazioni sui piani di ripristino](../site-recovery/recovery-plan-overview.md).

## <a name="disaster-recovery-testing"></a>Test del ripristino di emergenza

Dopo aver completato la configurazione del ripristino di emergenza, è opportuno testare il piano per verificarne il funzionamento.

Ecco alcuni suggerimenti su come testare il piano:

- Se le macchine virtuali di test hanno accesso a Internet, verranno sostituite da qualsiasi host di sessione esistente per le nuove connessioni, ma tutte le connessioni esistenti all'host della sessione originale rimarranno attive. Assicurarsi che l'amministratore che esegue il test dissegni tutti gli utenti attivi prima di testare il piano. 
- È consigliabile eseguire solo i test di ripristino di emergenza completi durante una finestra di manutenzione per non compromettere gli utenti. È anche possibile usare un pool di host nell'ambiente di convalida per il test. 
- Verificare che il test includa tutte le app cruciali per l'azienda.
- È consigliabile eseguire il failover di un massimo di 100 VM alla volta. Se sono presenti più macchine virtuali, è consigliabile eseguirne il failover in batch di 10 minuti.

## <a name="next-steps"></a>Passaggi successivi

In caso di domande su come garantire la sicurezza dei dati oltre alla pianificazione per le interruzioni, vedere la [Guida alla sicurezza](security-guide.md).