<properties
   pageTitle="Distribuzione di Resource Manager e classica | Microsoft Azure"
   description="Vengono descritte le differenze tra il modello di distribuzione di Gestione risorse e il modello di distribuzione classico (o gestione dei servizi)."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse

In questo argomento, sono descritti i modelli di distribuzione di Azure Resource Manager e di distribuzione classica, lo stato delle risorse e il motivo per cui le risorse sono state distribuite con un metodo piuttosto che con un altro. Il modello di distribuzione di Resource Manager prevede importanti differenze rispetto al modello di distribuzione classica e i due modelli non sono completamente compatibili tra loro. Per semplificare la distribuzione e la gestione delle risorse, Microsoft consiglia di utilizzare Gestione risorse per le nuove risorse e, se possibile, distribuire di nuovo le risorse esistenti tramite Gestione risorse.

Per la maggior parte delle risorse, è possibile passare a Resource Manager senza problemi. Tuttavia, alcuni provider di risorse offrono due versioni della risorsa (uno per il modello classico e uno per la gestione risorse) a causa delle differenze architetturali tra i modelli. I provider di risorse che differenziano i due modelli sono:

- **Calcolo**: supporta le istanze di macchine virtuali e i set di disponibilità facoltativi.
- **Archiviazione**: supporta account di archiviazione obbligatori che archiviano dischi rigidi virtuali per le macchine virtuali, inclusi il sistema operativo e altri dischi dati.
- **Rete**: supporta le schede di rete, gli indirizzi IP di macchine virtuali e le subnet all'interno di reti virtuali obbligatori, nonché i servizi di bilanciamento del carico, gli indirizzi IP del servizio di bilanciamento del carico e i gruppi di sicurezza di rete facoltativi.

Per questi tipi di risorsa, è necessario essere consapevoli della versione in uso poiché le operazioni supportate variano. Se si è pronti per eseguire la migrazione delle risorse dalla distribuzione classica alla distribuzione di Azure Resource Manager vedere [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md).

Per comprendere il modello utilizzato per distribuire le risorse, è opportuno approfondire i due modelli.

## Caratteristiche di Gestione risorse

Le risorse create tramite Gestione risorse condividono le caratteristiche seguenti:

- Creato tramite uno dei metodi seguenti:

  - Il [portale di Azure](https://portal.azure.com/).

        ![Azure portal](./media/resource-manager-deployment-model/preview-portal.png)

        Per le risorse di calcolo, archiviazione e rete, è possibile usare sia Resource Manager che la distribuzione classica. Selezionare **Gestione risorse**.

        ![Resource Manager deployment](./media/resource-manager-deployment-model/select-resource-manager.png)

  - Per Azure PowerShell, utilizzare la versione di Resource Manager dei comandi. Questi comandi hanno il formato *Verb-AzureRmNoun*, come illustrato di seguito.

            Get-AzureRmResourceGroupDeployment

  - [API REST di Gestione risorse di Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) per le operazioni REST.
  - Comandi Azure CLI eseguiti nella modalità **arm**.

            azure config mode arm

- Il tipo di risorsa non include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione**.

    ![App Web](./media/resource-manager-deployment-model/resource-manager-type.png)

L'applicazione illustrata nel diagramma seguente mostra come le risorse distribuite tramite Gestione risorse sono contenute in un unico gruppo di risorse.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Esistono inoltre relazioni tra le risorse all'interno dei provider di risorse:

- Una macchina virtuale dipende da un account di archiviazione specifico definito nel provider SRP per l'archiviazione dei relativi dischi nell'archiviazione BLOB (obbligatorio).
- Una macchina virtuale fa riferimento a una scheda di interfaccia di rete (NIC) specifica definita nel provider NRP (obbligatorio) e un set di disponibilità definito nel CRP (facoltativo).
- Una NIC fa riferimento all'indirizzo IP assegnato della macchina virtuale (obbligatorio), alla subnet della rete virtuale per la macchina virtuale (obbligatorio) e a un gruppo di sicurezza di rete (facoltativo).
- Una subnet all'interno di una rete virtuale fa riferimento a un gruppo di sicurezza di rete (facoltativo).
- Un'istanza del servizio di bilanciamento del carico fa riferimento al pool back-end di indirizzi IP che includono la NIC di una macchina virtuale (facoltativo) e fa riferimento a un indirizzo IP pubblico o privato del servizio di bilanciamento del carico (facoltativo).

## Caratteristiche della distribuzione classica

È inoltre possibile conoscere il modello di distribuzione classico come modello di gestione dei servizi.

In Gestione servizi di Azure, le risorse di calcolo, di archiviazione o di rete per l'hosting delle macchine virtuali sono fornite da:

- Un servizio cloud obbligatorio che funge da contenitore per l'hosting di macchine virtuali (calcolo). Le macchine virtuali sono fornite automaticamente con una scheda di rete (NIC) e un indirizzo IP assegnati da Azure. Il servizio cloud contiene inoltre un'istanza del servizio di bilanciamento del carico esterno, un indirizzo IP pubblico ed endpoint predefiniti per consentire il traffico di desktop remoto e di PowerShell remoto per le macchine virtuali basate su Windows e il traffico Secure Shell (SSH) per le macchine virtuali basate su Linux.
- Un account di archiviazione obbligatorio per l'archiviazione dei dischi rigidi virtuali per una macchina virtuale, inclusi il sistema operativo, i dati temporanei e altri dischi dati (archiviazione).
- Una rete virtuale facoltativa che funge da contenitore aggiuntivo, in cui è possibile creare una struttura di subnet e definire la subnet in cui si trova la macchina virtuale (rete).

Le risorse create nel modello di distribuzione classica condividono le caratteristiche seguenti:

- Creato tramite uno dei metodi seguenti:

  - [Portale classico](https://manage.windowsazure.com)

        ![Classic portal](./media/resource-manager-deployment-model/azure-portal.png)

        Or, the Azure portal and you specify **Classic** deployment (for Compute, Storage, and Networking).

        ![Classic deployment](./media/resource-manager-deployment-model/select-classic.png)

  - Per Azure PowerShell, utilizzare la versione di Gestione servizi dei comandi. Questi nomi di comando hanno il formato *Verb-AzureNoun*, come illustrato di seguito.

            Get-AzureDeployment

  - [API REST di Gestione servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) per le operazioni REST.
  - Comandi dell'interfaccia della riga di comando di Azure eseguiti in modalità **asm** o predefinita.
- Il tipo di risorsa include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione (classico)**.

    ![tipo classico](./media/resource-manager-deployment-model/classic-type.png)

È tuttavia possibile usare il portale di Azure per gestire le risorse che sono state create con la distribuzione classica.

Questi sono i componenti e le relative relazioni per Gestione servizi di Azure.

  ![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Vantaggi dell'utilizzo di Gestione risorse e di gruppi di risorse

Gestione risorse ha aggiunto il concetto di gruppo di risorse. Tutte le risorse create tramite Gestione risorse esiste all'interno di un gruppo di risorse. Il modello di distribuzione di Resource Manager offre diversi vantaggi:

- È possibile distribuire, gestire e monitorare tutti i servizi per la soluzione come un gruppo, anziché gestire singolarmente questi servizi.
- è possibile distribuire l'applicazione in tutto il ciclo di vita dell'app ripetutamente e avere la certezza che le risorse vengano distribuite in uno stato coerente.
- È possibile utilizzare modelli dichiarativi per definire la distribuzione.
- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.
- È possibile applicare il controllo di accesso a tutte le risorse nel gruppo di risorse perché il controllo degli accessi in base al ruolo è integrato in modo nativo nella piattaforma di gestione.
- È possibile applicare i tag alle risorse per organizzare logicamente tutte le risorse nella sottoscrizione.


Ogni risorsa creata da una distribuzione classica prima della Gestione risorse, non esiste all'interno di un gruppo di risorse. Quando è stato aggiunto Gestione risorse, tutte le risorse retroattive sono state aggiunti ai gruppi di risorse predefiniti. Se ora si crea una risorsa tramite la distribuzione classica, la risorsa viene creata automaticamente all'interno di un gruppo di risorse predefinito per tale servizio anche se non è stato specificato il gruppo di risorse in fase di distribuzione. Tuttavia, il solo fatto di esistere all'interno di un gruppo di risorse non significa che la risorsa è stata convertita nel modello di Gestione risorse. Per macchine virtuali, archiviazione e reti virtuali, se la risorsa è stata creata da una distribuzione classica, è necessario continuare a operare su di essa tramite operazioni classiche.

È possibile spostare le risorse a un gruppo di risorse diverso e aggiungere nuove risorse al gruppo di risorse. Pertanto, il gruppo di risorse può contenere una combinazione di risorse creato tramite Gestione risorse e distribuzione classica. Questa combinazione di risorse può produrre risultati imprevisti perché le risorse non supportano le stesse operazioni.

Utilizzando i modelli dichiarativi, potrebbe essere in grado di semplificare gli script per la distribuzione. Anziché tentare di convertire gli script esistenti dalla gestione dei servizi di gestione risorse, considerare la rielaborazione della strategia di distribuzione per sfruttare i vantaggi di definire l'infrastruttura e la configurazione del modello.

## Utilizzo di tag

I Tag consentono di organizzare logicamente le risorse. Solo le risorse create tramite il tag di supporto di Gestione risorse. Non è possibile applicare i tag alle risorse classiche.

Per altre informazioni sull'uso dei tag in Gestione risorse, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## Operazioni supportate per i modelli di distribuzione

Le risorse create con il modello di distribuzione classica non supportano le operazioni di Gestione risorse. In alcuni casi, un comando di Gestione risorse può recuperare informazioni su una risorsa creata da una distribuzione classica o può eseguire attività amministrative, quali lo spostamento di una risorsa classica a un altro gruppo di risorse, ma questi casi non dovrebbero dare l'impressione che il tipo supporti operazioni di Gestione risorse. Ad esempio, si supponga di avere un gruppo di risorse che contiene una macchina virtuale creata con distribuzione classica. Se si esegue il seguente comando PowerShell:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Verrà restituita la macchina virtuale:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Tuttavia il cmdlet **Get-AzureRmVM** restituisce solo le macchine virtuali distribuite tramite Resource Manager. Il comando seguente non restituisce la macchina virtuale creata tramite distribuzione classica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

In generale, non è possibile aspettarsi che le risorse create da una distribuzione classica funzionino con i comandi di Gestione risorse.

Quando si utilizzano le risorse create tramite Gestione risorse, è necessario utilizzare le operazioni di Gestione risorse e non tornare a operazioni di gestione del servizio.

## Considerazioni sulle macchine virtuali

Esistono alcune importanti considerazioni da tenere presente quando si usano le macchine virtuali.

- Le macchine virtuali distribuite con il modello di distribuzione classica non possono essere incluse in una rete virtuale distribuita con Gestione risorse.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.

Se si è pronti per eseguire la migrazione delle risorse dalla distribuzione classica alla distribuzione di Azure Resource Manager vedere [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md).

Per ulteriori informazioni sul passaggio di calcolo, l’archiviazione e le risorse di rete, vedere [Provider di calcolo, rete e di archiviazione in Gestione risorse di Microsoft Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md).

Per informazioni sulla connessione di reti virtuali da diversi modelli di distribuzione, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](./virtual-network/virtual-networks-arm-asm-s2s.md).

## Passaggi successivi

- Per la procedura dettagliata di creazione del modello che definisce una macchina virtuale, un account di archiviazione e una rete virtuale vedere [Procedura dettagliata per un modello di Azure Resource Manager](resource-manager-template-walkthrough.md).
- Per informazioni sulla struttura dei modelli di Resource Manager vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
- Per vedere i comandi per la distribuzione di un modello, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0720_2016-->