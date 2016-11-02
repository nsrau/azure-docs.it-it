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
   ms.date="07/29/2016"
   ms.author="tomfitz"/>

# Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse

In questo argomento, sono descritti i modelli di distribuzione di Azure Resource Manager e di distribuzione classica, lo stato delle risorse e il motivo per cui le risorse sono state distribuite con un metodo piuttosto che con un altro. Il modello di distribuzione di Resource Manager prevede importanti differenze rispetto al modello di distribuzione classica e i due modelli non sono completamente compatibili tra loro. Per semplificare la distribuzione e la gestione delle risorse, Microsoft consiglia di utilizzare Gestione risorse per le nuove risorse e, se possibile, distribuire di nuovo le risorse esistenti tramite Gestione risorse.

Se non si conosce Resource Manager è necessario vedere prima la terminologia definita in [Panoramica di Azure Resource Manager](resource-group-overview.md).

## Cronologia dei modelli di distribuzione

In origine, Azure metteva a disposizione solo il modello di distribuzione classica. In questo modello ogni risorsa era indipendente, non era possibile raggruppare le risorse correlate. Era invece necessario verificare manualmente quali risorse componessero la soluzione o l'applicazione e ricordare di gestirle in un approccio coordinato. Per distribuire una soluzione era necessario creare ogni risorsa singolarmente tramite il portale classico oppure creare uno script che distribuiva tutte le risorse nell'ordine corretto. Per eliminare una soluzione era necessario eliminare ogni risorsa singolarmente. Non era semplice applicare e aggiornare i criteri di controllo di accesso per le risorse correlate. Non era infine possibile applicare i tag alle risorse per etichettarle con i termini che consentono di monitorare le risorse e gestire la fatturazione.

Nel 2014, Azure ha introdotto Resource Manager con il nuovo concetto di gruppo di risorse. Un gruppo di risorse è un contenitore di risorse che condividono un ciclo di vita comune. Il modello di distribuzione di Resource Manager offre diversi vantaggi:

- È possibile distribuire, gestire e monitorare tutti i servizi per la soluzione come un gruppo, anziché gestire singolarmente questi servizi.
- È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente.
- È possibile applicare il controllo di accesso a tutte le risorse nel gruppo di risorse e tali criteri vengono applicati automaticamente quando si aggiungono nuove risorse al gruppo di risorse.
- È possibile applicare i tag alle risorse per organizzare logicamente tutte le risorse nella sottoscrizione.
- È possibile usare JavaScript Object Notation (JSON) per definire l'infrastruttura per la soluzione. Il file JSON è noto come modello di Resource Manager.
- È possibile definire le dipendenze tra risorse e pertanto esse vengono distribuite nell'ordine corretto.

Quando è stato aggiunto Gestione risorse, tutte le risorse retroattive sono state aggiunti ai gruppi di risorse predefiniti. Se ora si crea una risorsa tramite la distribuzione classica, la risorsa viene creata automaticamente all'interno di un gruppo di risorse predefinito per tale servizio anche se non è stato specificato il gruppo di risorse in fase di distribuzione. Tuttavia, il solo fatto di esistere all'interno di un gruppo di risorse non significa che la risorsa è stata convertita nel modello di Gestione risorse. La sezione successiva illustra il modo in cui ogni servizio gestisce i due modelli di distribuzione.

## Informazioni sul supporto dei modelli 

Quando si decide quale modello di distribuzione usare per le risorse, esistono tre scenari da tenere presenti:

1. Il servizio supporta Resource Manager e offre un solo tipo di distribuzione.
2. Il servizio supporta Resource Manager, ma offre due tipi di distribuzione, uno per Resource Manager e uno per la distribuzione classica. Si applica solo alle macchine virtuali, agli account di archiviazione e alle reti virtuali.
3. Il servizio non supporta Resource Manager.

Per verificare se un servizio supporta Resource Manager, vedere [Provider, aree, versioni API e schemi di Resource Manager](resource-manager-supported-services.md).

Se il servizio che si vuole usare non supporta Resource Manager è necessario continuare a usare la distribuzione classica.

Se il servizio supporta Resource Manager e **non è** una macchina virtuale, un account di archiviazione o una rete virtuale, è possibile usare Resource Manager senza problemi.

Per le macchine virtuali, gli account di archiviazione e le reti virtuali, se la risorsa è stata creata con una distribuzione classica è necessario continuare eseguire operazioni classiche sulla risorsa. Se per creare la macchina virtuale, l'account di archiviazione o la rete virtuale è stata usata la distribuzione di Resource Manager è necessario continuare a eseguire operazioni di Resource Manager. Questa distinzione può creare confusione soprattutto quando la sottoscrizione contiene una combinazione di risorse create con Resource Manager e la distribuzione classica. Questa combinazione di risorse può produrre risultati imprevisti perché le risorse non supportano le stesse operazioni.

In alcuni casi, un comando di Resource Manager può recuperare informazioni su una risorsa creata con una distribuzione classica o può eseguire attività amministrative, quali lo spostamento di una risorsa classica a un altro gruppo di risorse, ma non per questo il tipo supporta le operazioni di Resource Manager. Si supponga ad esempio di avere un gruppo di risorse che contiene una macchina virtuale creata con la distribuzione classica. Se si esegue questo comando PowerShell di Resource Manager:

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Verrà restituita la macchina virtuale:
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Il cmdlet **Get-AzureRmVM** di Resource Manager restituisce tuttavia solo le macchine virtuali distribuite con Resource Manager. Il comando seguente non restituisce la macchina virtuale creata tramite distribuzione classica.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Esistono altre importanti considerazioni da tenere presenti quando si usano le macchine virtuali.

- Le macchine virtuali distribuite con il modello di distribuzione classica non possono essere incluse in una rete virtuale distribuita con Gestione risorse.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.
- Le macchine virtuali distribuite con il modello di distribuzione di Gestione risorse deve essere incluso in una rete virtuale.

Per informazioni sulla connessione di reti virtuali da diversi modelli di distribuzione, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](./virtual-network/virtual-networks-arm-asm-s2s.md).

Solo le risorse create tramite il tag di supporto di Gestione risorse. Non è possibile applicare i tag alle risorse classiche. Per altre informazioni sull'uso dei tag in Gestione risorse, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

## Caratteristiche di Gestione risorse

Per comprendere i due modelli è opportuno esaminare le caratteristiche dei tipi di Resource Manager:

- Risorse create tramite il [portale di Azure](https://portal.azure.com/).

     ![Portale di Azure](./media/resource-manager-deployment-model/portal.png)

     Per le risorse di calcolo, archiviazione e rete, è possibile usare sia Resource Manager che la distribuzione classica. Selezionare **Gestione risorse**.

     ![Distribuzione di Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)

- Risorse create con la versione dei cmdlet Azure PowerShell di Resource Manager. Questi comandi hanno il formato *Verb-AzureRmNoun*, come illustrato di seguito.

        New-AzureRmResourceGroupDeployment

- Risorse create tramite l'[API REST di Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790568.aspx) per le operazioni REST.

- Risorse create tramite i comandi dell'interfaccia della riga di comando di Azure nella modalità **arm**.

        azure config mode arm
        azure group deployment create 

- Il tipo di risorsa non include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione**.

    ![App Web](./media/resource-manager-deployment-model/resource-manager-type.png)

L'applicazione illustrata nel diagramma seguente mostra come le risorse distribuite tramite Gestione risorse sono contenute in un unico gruppo di risorse.

  ![Architettura di Resource Manager](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Esistono inoltre relazioni tra le risorse all'interno dei provider di risorse:

- Una macchina virtuale dipende da un account di archiviazione specifico definito nel provider SRP per l'archiviazione dei relativi dischi nell'archiviazione BLOB (obbligatorio).
- Una macchina virtuale fa riferimento a una scheda di interfaccia di rete (NIC) specifica definita nel provider NRP (obbligatorio) e un set di disponibilità definito nel CRP (facoltativo).
- Una NIC fa riferimento all'indirizzo IP assegnato della macchina virtuale (obbligatorio), alla subnet della rete virtuale per la macchina virtuale (obbligatorio) e a un gruppo di sicurezza di rete (facoltativo).
- Una subnet all'interno di una rete virtuale fa riferimento a un gruppo di sicurezza di rete (facoltativo).
- Un'istanza del servizio di bilanciamento del carico fa riferimento al pool back-end di indirizzi IP che includono la NIC di una macchina virtuale (facoltativo) e fa riferimento a un indirizzo IP pubblico o privato del servizio di bilanciamento del carico (facoltativo).

## Caratteristiche della distribuzione classica

È inoltre possibile conoscere il modello di distribuzione classico come modello di gestione dei servizi.

Le risorse create nel modello di distribuzione classica condividono le caratteristiche seguenti:

- Risorse create tramite il [portale classico](https://manage.windowsazure.com)

     ![Portale classico](./media/resource-manager-deployment-model/classic-portal.png)

     oppure il portale di Azure specificando la distribuzione **Classica** per i servizi di calcolo, archiviazione e rete.

     ![Distribuzione classica](./media/resource-manager-deployment-model/select-classic.png)

- Risorse create con la versione dei cmdlet Azure PowerShell di Gestione dei servizi. Questi nomi di comando hanno il formato *Verb-AzureNoun*, come illustrato di seguito.

        New-AzureVM 

- Risorse create tramite l'[API REST Gestione dei servizi](https://msdn.microsoft.com/library/azure/ee460799.aspx) per le operazioni REST.
- Risorse create tramite i comandi dell'interfaccia della riga di comando di Azure nella modalità **asm**.

        azure config mode asm
        azure vm create 

- Il tipo di risorsa include **(classico)** nel nome. L'immagine seguente mostra il tipo come **Account di archiviazione (classico)**.

    ![tipo classico](./media/resource-manager-deployment-model/classic-type.png)

È tuttavia possibile usare il portale di Azure per gestire le risorse che sono state create con la distribuzione classica.

In Gestione servizi di Azure, le risorse di calcolo, di archiviazione o di rete per l'hosting delle macchine virtuali sono fornite da:

- Un servizio cloud obbligatorio che funge da contenitore per l'hosting di macchine virtuali (calcolo). Le macchine virtuali sono fornite automaticamente con una scheda di rete (NIC) e un indirizzo IP assegnati da Azure. Il servizio cloud contiene inoltre un'istanza del servizio di bilanciamento del carico esterno, un indirizzo IP pubblico ed endpoint predefiniti per consentire il traffico di desktop remoto e di PowerShell remoto per le macchine virtuali basate su Windows e il traffico Secure Shell (SSH) per le macchine virtuali basate su Linux.
- Un account di archiviazione obbligatorio per l'archiviazione dei dischi rigidi virtuali per una macchina virtuale, inclusi il sistema operativo, i dati temporanei e altri dischi dati (archiviazione).
- Una rete virtuale facoltativa che funge da contenitore aggiuntivo, in cui è possibile creare una struttura di subnet e definire la subnet in cui si trova la macchina virtuale (rete).

Questi sono i componenti e le relative relazioni per Gestione servizi di Azure.

  ![architettura classica](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Migrazione dal modello classico a Resource Manager

Se si è pronti per eseguire la migrazione delle risorse dalla distribuzione classica alla distribuzione di Resource Manager, vedere:

1. [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migrazione supportata dalla piattaforma di risorse IaaS dal modello classico al modello di Azure Resource Manager](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite Azure PowerShell](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Eseguire la migrazione di risorse IaaS dal modello classico al modello di Azure Resource Manager tramite l'interfaccia della riga di comando di Azure](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## Passaggi successivi

- Per la procedura dettagliata di creazione del modello che definisce una macchina virtuale, un account di archiviazione e una rete virtuale, vedere [Procedura dettagliata per un modello di Resource Manager](resource-manager-template-walkthrough.md).
- Per informazioni sulla struttura dei modelli di Resource Manager, vedere [Creazione di modelli di Azure Resource Manager](resource-group-authoring-templates.md).
- Per vedere i comandi per la distribuzione di un modello, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0803_2016-->