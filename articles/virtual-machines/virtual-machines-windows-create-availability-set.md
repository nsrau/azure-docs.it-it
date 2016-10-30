<properties
    pageTitle="Creare un set di disponibilità per una VM | Microsoft Azure"
    description="Informazioni su come creare un set di disponibilità per le macchine virtuali tramite il portale di Azure o PowerShell e utilizzando il modello di distribuzione di Resource Manager."
    keywords="set di disponibilità"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="create-an-availability-set"></a>Creare un set di disponibilità 

Quando si usa il portale, se si desidera includere la VM in un set di disponibilità, per prima cosa è necessario creare quest'ultimo.

Per altre informazioni sulla creazione e sull'uso dei set di disponibilità, vedere l'articolo relativo alla [gestione della disponibilità delle macchine virtuali](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utilizzare il portale per creare un set di disponibilità prima di creare le VM

1. Nel menu dell'hub, fare clic su **Sfoglia** e selezionare **Set di disponibilità**.

2. Nel pannello **Set di disponibilità** fare clic su **Aggiungi**.

    ![Schermata che mostra il pulsante Aggiungi per creare un nuovo set di disponibilità.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Nel pannello **Crea set di disponibilità** compilare le informazioni sul set.

    ![Schermata che mostra le informazioni da immettere per creare il set di disponibilità.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nome** : il nome deve contenere da 1 a 80 caratteri, tra numeri, lettere, punti, caratteri di sottolineatura e trattini. Il primo carattere deve essere una lettera o un numero. L'ultimo carattere deve essere una lettera o un numero o un carattere di sottolineatura.
    - **Domini di errore** : i domini di errore definiscono il gruppo di macchine virtuali che condividono una fonte di alimentazione e uno switch di rete comuni. Per impostazione predefinita, le VM sono distribuite in un massimo di tre domini di errore; per la distribuzione possono essere usati da 1 a 3 domini.
    - **Domini di aggiornamento**: per impostazione predefinita vengono assegnati cinque domini di aggiornamento, ma è possibile impostarne da 1 a 20. I domini di aggiornamento indicano gruppi di macchine virtuali e l'hardware fisico sottostante che possono essere riavviati nello stesso momento. Ad esempio, se si specificano cinque domini di aggiornamento, quando in un set di disponibilità vengono configurate più di cinque macchine virtuali, la sesta macchina viene inserita nello stesso dominio di aggiornamento della prima, la settima nel dominio di aggiornamento della seconda e così via. L'ordine delle operazioni di riavvio potrebbe non essere sequenziale, ma verrà riavviato un solo dominio di aggiornamento alla volta.
    - **Sottoscrizione** : se sono disponibili più sottoscrizioni, selezionare quella da usare.
    - **Gruppo di risorse**: per selezionare un gruppo di risorse esistente, fare clic sulla freccia verso il basso e scegliere un gruppo di risorse dall'elenco. È inoltre possibile creare un nuovo gruppo di risorse digitando un nome. Il nome può contenere lettere, numeri, punti, trattini, caratteri di sottolineatura e parentesi aperte o chiuse, mentre non può terminare con un punto. Tutte le VM nel gruppo di disponibilità devono essere create nello stesso gruppo di risorse del set di disponibilità.
    - **Percorso** : selezionare un percorso nell'elenco a discesa.

4. Dopo aver immesso le informazioni, fare clic su **Crea**. Una volta creato, il gruppo di disponibilità verrà visualizzato nell'elenco dopo aver aggiornato il portale.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Usare il portale per creare una macchina virtuale e un set di disponibilità contemporaneamente

Quando si crea una nuova VM tramite il portale, è anche possibile creare un nuovo set di disponibilità durante la creazione della prima VM del set.

![Schermata che illustra la procedura di creazione di un nuovo set di disponibilità in fase di creazione della VM.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Aggiungere una nuova VM a un set di disponibilità esistente

Per ogni nuova VM da aggiungere al set, assicurarsi di creare la VM nello stesso **gruppo di risorse** e di selezionare il set di disponibilità esistente al Passaggio 3. 

![Schermata che illustra come selezionare un set di disponibilità da utilizzare per la VM.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Usare PowerShell per creare un set di disponibilità

In questo esempio viene creato un set di disponibilità nel gruppo di risorse **RMResGroup** nella posizione **Stati Uniti occidentali**. Questa operazione deve essere eseguita prima di creare la prima VM da inserire nel set.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Per ulteriori informazioni, vedere [New AzureRmAvailabilitySet](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Risoluzione dei problemi

- Quando si crea una VM, se il set di disponibilità desiderato non si trova nell'elenco a discesa nel portale, potrebbe essere stato creato in un altro gruppo di risorse. Se non si conosce il gruppo di risorse del set di disponibilità, accedere al menu dell'hub e fare clic su Sfoglia > Gruppi di disponibilità per visualizzare un elenco dei set di disponibilità e dei gruppi di risorse appartenenti a ciascuno.


## <a name="next-steps"></a>Passaggi successivi

Aumentare lo spazio di archiviazione della VM aggiungendo un ulteriore [disco dati](virtual-machines-windows-attach-disk-portal.md).



<!--HONumber=Oct16_HO2-->


