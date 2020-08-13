---
title: Abilitare la replica per i computer locali con endpoint privati
description: Questo articolo descrive come configurare la replica per i computer locali usando endpoint privati in Site Recovery.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 13c19f07ac21f986a5523407e46c59c050ebf96d
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142078"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Eseguire la replica di computer locali usando endpoint privati

Azure Site Recovery consente di usare endpoint privati di [collegamento privato di Azure](../private-link/private-endpoint-overview.md) per replicare i computer locali in una rete virtuale in Azure. Il supporto per l'accesso dell'endpoint privato a un insieme di credenziali di ripristino è supportato in queste aree:

- Azure commerciale: Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti orientali
- Azure per enti pubblici: US Gov Virginia, US Gov Arizona, US Gov Texas, US DoD (area orientale), US DoD (area centrale)

In questo articolo viene descritto come completare i passaggi seguenti:

- Creare un insieme di credenziali di servizi di ripristino di backup di Azure per proteggere i computer.
- Abilitare un'identità gestita per l'insieme di credenziali. Concedere le autorizzazioni necessarie per accedere agli account di archiviazione per abilitare la replica del traffico da locale a percorsi di destinazione di Azure. L'accesso alle identità gestite per l'archiviazione è necessario per l'accesso al collegamento privato all'insieme di credenziali.

- Apportare le modifiche DNS necessarie per gli endpoint privati.
- Creare e approvare endpoint privati per un insieme di credenziali all'interno di una rete virtuale.
- Creare endpoint privati per gli account di archiviazione. È possibile continuare a consentire l'accesso pubblico o con firewall per l'archiviazione in base alle esigenze. La creazione di un endpoint privato per accedere all'archiviazione non è necessaria per Azure Site Recovery.
  
Il diagramma seguente illustra il flusso di lavoro di replica per il ripristino di emergenza ibrido con endpoint privati. Non è possibile creare endpoint privati nella rete locale. Per usare i collegamenti privati, è necessario creare una rete virtuale di Azure (chiamata *rete di bypass* in questo articolo), stabilire la connettività privata tra l'ambiente locale e la rete di bypass, quindi creare endpoint privati nella rete di bypass. È possibile scegliere qualsiasi tipo di connettività privata.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Diagramma che illustra l'architettura per gli endpoint Azure Site Recovery e privati.":::

## <a name="prerequisites-and-caveats"></a>Prerequisiti e avvertenze

- I collegamenti privati sono supportati in Site Recovery 9,35 e versioni successive.
- È possibile creare endpoint privati solo per i nuovi insiemi di credenziali dei servizi di ripristino che non hanno elementi registrati. Pertanto, è necessario creare endpoint privati prima di aggiungere elementi all'insieme di credenziali. Per informazioni sui prezzi, vedere [prezzi di collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link/) .
- Quando si crea un endpoint privato per un insieme di credenziali, l'insieme di credenziali viene bloccato. È possibile accedervi solo da reti con endpoint privati.
- Azure Active Directory attualmente non supporta endpoint privati. Quindi, è necessario consentire l'accesso in uscita dalla rete virtuale di Azure protetta agli indirizzi IP e ai nomi di dominio completi necessari per il funzionamento di Azure Active Directory in un'area.
  Come applicabile, è anche possibile usare il tag del gruppo di sicurezza di rete "Azure Active Directory" e i tag del firewall di Azure per consentire l'accesso ai Azure Active Directory.
- Sono necessari cinque indirizzi IP nella rete di bypass in cui si crea l'endpoint privato. Quando si crea un endpoint privato per l'insieme di credenziali, Site Recovery crea cinque collegamenti privati per l'accesso ai relativi microservizi.
- Per la connettività degli endpoint privati a un account di archiviazione della cache è necessario un indirizzo IP aggiuntivo nella rete di bypass. È possibile usare qualsiasi metodo di connettività tra l'endpoint locale e l'endpoint dell'account di archiviazione. Ad esempio, è possibile usare Internet o Azure [ExpressRoute](../expressroute/index.yml). La definizione di un collegamento privato è facoltativa. È possibile creare endpoint privati per l'archiviazione solo su account per utilizzo generico V2. Per informazioni sui prezzi per il trasferimento di dati negli account per utilizzo generico V2, vedere [prezzi dei BLOB di pagine di Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) .

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Creare e usare endpoint privati per Site Recovery

 Le sezioni seguenti descrivono i passaggi da eseguire per creare e usare endpoint privati per Site Recovery nelle reti virtuali.

> [!NOTE]
> Si consiglia di seguire questi passaggi nell'ordine indicato. In caso contrario, potrebbe non essere possibile usare endpoint privati nell'insieme di credenziali e potrebbe essere necessario riavviare il processo con un nuovo insieme di credenziali.

### <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali di servizi di ripristino contiene le informazioni di replica delle macchine virtuali. Viene usato per attivare Site Recovery operazioni. Per informazioni su come creare un insieme di credenziali di servizi di ripristino nell'area di Azure in cui si vuole eseguire il failover in caso di emergenza, vedere creare un insieme di credenziali [di servizi di ripristino](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Abilitare l'identità gestita per l'insieme di credenziali

Un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) consente all'insieme di credenziali di accedere agli account di archiviazione. Site Recovery potrebbe dover accedere agli account di archiviazione di destinazione e cache/log, a seconda dei requisiti. L'accesso a identità gestite è obbligatorio quando si usa il servizio di collegamento privato per l'insieme di credenziali.

1. Passare all'insieme di credenziali di servizi di ripristino. Selezionare **Identity** in **Settings (impostazioni**):

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Screenshot che mostra la pagina delle impostazioni di identità.":::

1. Modificare lo **stato** **su on** e selezionare **Salva**.

   Viene generato un ID oggetto. L'insieme di credenziali è ora registrato con Azure Active Directory.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Creare endpoint privati per l'insieme di credenziali di servizi di ripristino

Per proteggere le macchine virtuali nella rete di origine locale, è necessario un endpoint privato per l'insieme di credenziali nella rete di bypass. Creare l'endpoint privato utilizzando il centro collegamenti privati nel portale di Azure o utilizzando [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Nella casella di ricerca portale di Azure cercare "collegamento privato". Selezionare **collegamento privato** per accedere a link Center privato:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Screenshot che mostra la ricerca nel portale di Azure per il centro collegamenti privati.":::

1. Nel riquadro sinistro selezionare **endpoint privati**. Nella pagina **endpoint privati** selezionare **Aggiungi** per avviare la creazione di un endpoint privato per l'insieme di credenziali:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Screenshot che illustra come creare un endpoint privato in un centro di collegamento privato.":::

1. Nella pagina **Crea un endpoint privato** specificare i dettagli per creare la connessione all'endpoint privato.

   1. **Nozioni di base**. Fornire i dettagli di base per gli endpoint privati. Usare l'area usata per la rete di bypass:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Screenshot che mostra la scheda di base per la creazione di un endpoint privato.":::

   1. **Risorsa**. In questa scheda è necessario specificare la risorsa piattaforma distribuita come servizio per la quale si desidera creare la connessione. In **tipo di risorsa** per la sottoscrizione selezionata selezionare **Microsoft. RecoveryServices/Vaults**. Scegliere il nome dell'insieme di credenziali di servizi di ripristino in **risorsa**. Selezionare **Azure Site Recovery** come **risorsa secondaria di destinazione**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Screenshot che mostra la scheda risorsa per il collegamento a un endpoint privato.":::

   1. **Configurazione**. In questa scheda specificare la rete di bypass e la subnet in cui si desidera creare l'endpoint privato. 

      Per abilitare l'integrazione con una zona DNS privata, selezionare **Sì**.
      Scegliere una zona DNS esistente o crearne una nuova. Se **si seleziona Sì** , la zona verrà collegata automaticamente alla rete di bypass. Questa azione aggiunge anche i record DNS necessari per la risoluzione DNS dei nuovi indirizzi IP e dei nomi di dominio completi creati per l'endpoint privato.

      Assicurarsi di scegliere di creare una nuova zona DNS per ogni nuovo endpoint privato che si connette allo stesso insieme di credenziali. Se si sceglie una zona DNS privata esistente, i record CNAME precedenti verranno sovrascritti. Vedere [informazioni aggiuntive sull'endpoint privato](../private-link/private-endpoint-overview.md#private-endpoint-properties) prima di continuare.

      Se l'ambiente ha un modello hub e spoke, è necessario un solo endpoint privato e una sola zona DNS privata per l'intera installazione. Questo perché per tutte le reti virtuali è già abilitato il peering. Per altre informazioni, vedere [integrazione di DNS con endpoint privati](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Per creare manualmente la zona DNS privata, seguire la procedura descritta in [creare zone DNS private e aggiungere manualmente i record DNS](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Screenshot che mostra la scheda configurazione per la configurazione di un endpoint privato.":::

   1. **Tag**. Facoltativamente, è possibile aggiungere tag per l'endpoint privato.

   1. **Esaminare \+ Crea**. Al termine della convalida, selezionare **Crea** per creare l'endpoint privato.

Quando viene creato l'endpoint privato, all'endpoint privato vengono aggiunti cinque nomi di dominio completi (FQDN). Questi collegamenti consentono ai computer della rete locale di accedere tramite la rete di bypass, tutti i microservizi Site Recovery richiesti nel contesto dell'insieme di credenziali. È possibile usare lo stesso endpoint privato per la protezione di qualsiasi macchina di Azure nella rete di bypass e in tutte le reti con peering.

I cinque nomi di dominio sono formattati in questo modello:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Approva endpoint privati per Site Recovery

Se si crea l'endpoint privato e si è anche il proprietario dell'insieme di credenziali di servizi di ripristino, l'endpoint privato creato in precedenza viene approvato automaticamente entro pochi minuti. In caso contrario, il proprietario dell'insieme di credenziali deve approvare l'endpoint privato prima di poterlo usare. Per approvare o rifiutare una connessione all'endpoint privato richiesta, passare a **connessioni a endpoint privati** in **Impostazioni** nella pagina dell'insieme di credenziali di ripristino.

È possibile passare alla risorsa endpoint privato per esaminare lo stato della connessione prima di continuare:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Screenshot che mostra la pagina connessioni endpoint privato dell'insieme di credenziali e l'elenco di connessioni.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Opzionale Creare endpoint privati per l'account di archiviazione della cache

È possibile usare un endpoint privato per archiviazione di Azure. La creazione di endpoint privati per l'accesso all'archiviazione è facoltativa per la replica Azure Site Recovery. Se si crea un endpoint privato per l'archiviazione, è necessario un endpoint privato per l'account di archiviazione cache/log nella rete virtuale di bypass.

> [!NOTE]
> Gli endpoint privati per l'archiviazione possono essere creati solo negli account di archiviazione per utilizzo generico V2. Per informazioni sui prezzi, vedere [prezzi dei BLOB di pagine di Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Seguire le [istruzioni per la creazione di un'archiviazione privata](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) per creare un account di archiviazione con un endpoint privato. Assicurarsi di selezionare **Sì** in **integrazione con la zona DNS privata**. Selezionare una zona DNS esistente o crearne una nuova.

### <a name="grant-required-permissions-to-the-vault"></a>Concedere le autorizzazioni necessarie all'insieme di credenziali

A seconda della configurazione, potrebbero essere necessari uno o più account di archiviazione nell'area di Azure di destinazione. A questo punto, concedere le autorizzazioni di identità gestite per tutti gli account di archiviazione cache/log necessari per Site Recovery. In questo caso, è necessario creare in anticipo gli account di archiviazione richiesti.

Prima di abilitare la replica delle macchine virtuali, l'identità gestita dell'insieme di credenziali deve avere le autorizzazioni seguenti per il ruolo, a seconda del tipo di account di archiviazione.

- Account di archiviazione basati su Gestione risorse (tipo standard):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Collaboratore ai dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Account di archiviazione basati su Gestione risorse (tipo Premium):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietario dei dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Account di archiviazione classici:
  - [Collaboratore account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Questi passaggi descrivono come aggiungere un'assegnazione di ruolo all'account di archiviazione:

1. Passare all'account di archiviazione. Selezionare **controllo di accesso (IAM)** nel riquadro sinistro.

1. Nella sezione **aggiungere un'assegnazione di ruolo** selezionare **Aggiungi**:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Screenshot che mostra la pagina controllo di accesso (IAM) per un account di archiviazione.":::

1. Nell'elenco **ruolo** della pagina **Aggiungi assegnazione ruolo** selezionare il ruolo dall'elenco all'inizio di questa sezione. Immettere il nome dell'insieme di credenziali e quindi selezionare **Save (Salva**).

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Screenshot che mostra la pagina Aggiungi assegnazione ruolo.":::

Dopo aver aggiunto queste autorizzazioni, è necessario consentire l'accesso a servizi attendibili Microsoft. Passare a **firewall e reti virtuali** e selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** in **eccezioni**.

### <a name="protect-your-virtual-machines"></a>Proteggi le tue macchine virtuali

Al termine delle attività precedenti, continuare con l'installazione dell'infrastruttura locale. Per continuare, completare una delle attività seguenti: 

- [Distribuire un server di configurazione per VMware e computer fisici](./vmware-azure-deploy-configuration-server.md)
- [Configurare l'ambiente Hyper-V per la replica](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Al termine dell'installazione, abilitare la replica per le macchine di origine. Non configurare l'infrastruttura solo dopo la creazione degli endpoint privati per l'insieme di credenziali nella rete di bypass.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Creare zone DNS private e aggiungere i record DNS manualmente

Se non è stata selezionata l'opzione per l'integrazione con una zona DNS privata quando è stato creato l'endpoint privato per l'insieme di credenziali, attenersi alla procedura descritta in questa sezione.

Creare una zona DNS privata per consentire al provider Site Recovery (per computer Hyper-V) o al server di elaborazione (per macchine virtuali VMware/computer fisici) di risolvere i nomi di dominio completi privati in indirizzi IP privati.

1. Creare una zona DNS privata.

   1. Cercare "zona DNS privata" nella casella di ricerca **tutti i servizi** e quindi selezionare **DNS privato zona** nei risultati:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Screenshot che mostra la ricerca di una zona DNS privata nella pagina nuove risorse della portale di Azure.":::

   1. Nella pagina **zone DNS privato** selezionare il pulsante **Aggiungi** per avviare la creazione di una nuova zona.

   1. Nella pagina **Crea zona DNS privata** immettere i dettagli richiesti. Immettere **privatelink.siterecovery.WindowsAzure.com** per il nome della zona DNS privata. È possibile scegliere qualsiasi gruppo di risorse e qualsiasi sottoscrizione.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Screenshot che mostra la scheda nozioni di base della pagina Crea zona DNS privato.":::

   1. Passare alla scheda **verifica \+ creazione** per esaminare e creare la zona DNS.

1. Collegare la zona DNS privata alla rete virtuale.

   È ora necessario collegare la zona DNS privata creata al bypass.

   1. Passare alla zona DNS privata creata nel passaggio precedente e quindi fare clic su **collegamenti rete virtuale** nel riquadro sinistro. Selezionare **Aggiungi**.

   1. Immettere i dettagli necessari. Negli elenchi **sottoscrizione** e **rete virtuale** Selezionare i dettagli che corrispondono alla rete di bypass. Lasciare i valori predefiniti negli altri campi.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Screenshot che mostra la pagina Aggiungi collegamento rete virtuale.":::

1. Aggiungere record DNS.

   A questo punto, dopo aver creato la zona DNS privata necessaria e l'endpoint privato, è necessario aggiungere i record DNS alla zona DNS.

   > [!NOTE]
   > Se si usa una zona DNS privata personalizzata, assicurarsi di creare voci simili, come descritto nel passaggio seguente.

   In questo passaggio è necessario creare voci per ogni FQDN nell'endpoint privato nella zona DNS privata.

   1. Passare alla zona DNS privata e quindi passare alla sezione **Panoramica** nel riquadro sinistro. Selezionare **set di record** per iniziare ad aggiungere i record.

   1. Nella pagina **Aggiungi set di record** aggiungere una voce per ogni nome di dominio completo e IP privato come record **di** tipo. In **Panoramica**è possibile ottenere un elenco dei nomi di dominio completi e degli indirizzi IP nella pagina **endpoint privato** . Come si può notare nello screenshot seguente, il primo nome di dominio completo dall'endpoint privato viene aggiunto al set di record nella zona DNS privata.

      Questi nomi di dominio completi corrispondono a questo modello:`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Screenshot che mostra la pagina Aggiungi set di record.":::

## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati abilitati endpoint privati per la replica della macchina virtuale, vedere questi altri articoli per informazioni aggiuntive e correlate:

- [Distribuire un server di configurazione locale](./vmware-azure-deploy-configuration-server.md)
- [Configurare il ripristino di emergenza di macchine virtuali Hyper-V locali in Azure](./hyper-v-azure-tutorial.md)