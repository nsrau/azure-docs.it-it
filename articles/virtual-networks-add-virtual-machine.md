<properties  linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

<h1>Aggiunta di una macchina virtuale a Rete virtuale</h1>

<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

In questa esercitazione verranno descritte le procedure per creare un account di archiviazione di Azure e una macchina virtuale che verranno quindi aggiunti a una [rete virtuale][1].

In questa esercitazione si presuppone che l'utente non abbia mai utilizzato Azure.
 
<div  class="dev-callout"> 
<b>Importante</b>

<p>Se si intende creare una macchina virtuale per installare una nuova foresta Active Directory, seguire le istruzioni fornite in <a href="../active-directory-forest/">Installazione di una nuova foresta Active Directory in una rete virtuale di Azure</a>.</p>
</div>

## Obiettivi

In questa esercitazione si apprenderà come:

* [Creare un account di archiviazione](#CreateStorageAcct)

* [Creare una macchina virtuale e distribuirla in una rete virtuale](#CreateVM)

## Prerequisiti

* Avere completato una delle esercitazioni seguenti:
  
  * [Creazione di una rete virtuale in Azure](/en-us/manage/services/networking/create-a-virtual-network/)
    
    -OPPURE-
  * [Creazione di una rete virtuale per la connettività cross-premise](/en-us/manage/services/networking/cross-premises-connectivity/)

* Account Windows Live con almeno una sottoscrizione valida attiva.

* Nomi degli elementi seguenti recuperati dalle esercitazioni [Creazione di una rete virtuale in Azure](/en-us/manage/services/networking/create-a-virtual-network/) o [Creazione di una rete virtuale per la connettività cross-premise](/en-us/manage/services/networking/cross-premises-connectivity/):
  
  * Gruppo di affinità assegnato alla rete virtuale.
  
  * Nome della rete virtuale.
  
  * Nomi delle subnet.

## <a name="CreateStorageAcct">Creazione dell'account di archiviazione</a>

1.  Dopo avere creato la rete virtuale nel [portale di gestione di Azure][2], fare clic su **New** nell'angolo inferiore destro della schermata.
    
    ![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  Nel pannello di navigazione fare clic su **DATA SERVICES**, **STORAGE** e quindi su **QUICK CREATE**.
    
    ![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  Immettere le informazioni seguenti e quindi fare clic sul segno di spunta nella parte inferiore destra della schermata.

* **URL:** digitare *yourstorage*.

* **REGION/AFFINITY GROUP:** selezionare **YourAffinityGroup** nell'elenco a discesa.

* **ENABLE GEO-REPLICATION:** lasciare questa casella selezionata.
  
  ![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

4.  Nella pagina **Storage**, al termine del processo nella colonna **STATUS** verrà visualizzata l'indicazione **Online**.
    
    ![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)


## <a name="CreateVM">Creazione di una macchina virtuale e distribuzione in una rete virtuale</a>
**Per creare una macchina virtuale e distribuirla in una rete virtuale:**

1.  Dopo avere creato l'account di archiviazione, fare clic su **New** nell'angolo inferiore sinistro della schermata.
    
    ![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  Nel pannello di navigazione fare clic su **COMPUTE**, **VIRTUAL MACHINE** e quindi su **FROM GALLERY**.
    
    ![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  Nella schermata **VM OS Selection** selezionare **Windows Server 2008 R2 SP1, October 2012** (o la versione più recente disponibile) e quindi fare clic sulla freccia avanti.
    
    ![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)

4.  Nella pagina **Virtual machine configuration** immettere le informazioni seguenti e quindi fare clic sulla freccia avanti. <!-- SHOULD WE TELL USERS TO WRITE DOWN USER NAME AND PASS?? -->
    
    **Suggerimento:** prendere nota del nome utente e della password in quanto si tratta delle credenziali necessarie per accedere alla nuova macchina virtuale.

* **VIRTUAL MACHINE NAME:** digitare *YourVMachine*.

* **NEW USER NAME:** sola lettura.

* **NEW PASSWORD:** immettere una password complessa.

* **CONFIRM PASSWORD:** ridigitare la password.

* **SIZE:** selezionare **Small**.
  
	![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

5.  Nella schermata **Virtual machine mode** immettere le informazioni seguenti e quindi fare clic sulla freccia avanti.

* **Standalone Virtual Machine:** lasciare questa opzione selezionata.

* **DNS NAME:** digitare *yourcloudapplication*.

* **STORAGE ACCOUNT:** Selezionare **yourstorage**.

* **REGION/AFFINITY GROUP/VIRTUAL NETWORK:** selezionare **YourVirtualNetwork** nell'elenco a discesa.
  
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

6.  Nella schermata **Virtual machine options** immettere le informazioni seguenti e quindi fare clic sul pulsante con il segno di spunta. Verrà creata la macchina virtuale. La creazione della macchina virtuale potrebbe richiedere fino a 10 minuti.
    <!-- CONFIRM HOW LONG IT CAN TAKE ON AVG FOR VMACHINE TO BE CREATED -->

* **AVAILABILITY SET:** selezionare **none**.

* **VIRTUAL NETWORK SUBNETS:** selezionare **FrontEndSubnet**.
  
	<div class="dev-callout" markdown="1">
	<b>NOTA</b>

	<p>È consigliabile selezionare almeno una subnet e NON selezionare la	subnet del gateway.</p>
	</div>
  
	![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

7.  Al termine della creazione della macchina virtuale, nella schermata Virtual Machines, in **STATUS** verrà visualizzata l'indicazione **Running**.
    
    ![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)


8.  Nel pannello di navigazione fare clic su **ALL ITEMS**. Tutti gli oggetti creati verranno visualizzati con il relativo stato corrente.
    
    ![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## Passaggi successivi
Per installare un controller di dominio aggiuntivo per il dominio Active Directory locale nella macchina virtuale appena creata, vedere [Installazione di un controller di dominio Active Directory di replica in una rete virtuale di Azure](/en-us/manage/services/networking/replica-domain-controller/).

## Vedere anche

* [Rete virtuale di Azure][1]

* [Configurare una rete virtuale utilizzando file di configurazione di rete][3]

<!-- LINKS -->

[1]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[2]: http://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx