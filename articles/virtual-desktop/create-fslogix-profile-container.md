---
title: Contenitori di profili FSLogix NetApp Windows Virtual Desktop - Azure
description: Come creare un contenitore di profili FSLogix usando i file NetApp di Azure in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 272188b50fe59435031a4a2fb9c252f3f358bb6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535732"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Creare un contenitore di profili FSLogix per un pool host usando I file di Rete di AzureCreate an FSLogix profile container for a host pool using Azure NetApp Files

È consigliabile utilizzare i contenitori dei profili FSLogix come soluzione di profilo utente per il servizio Desktop virtuale di [Windows.](overview.md) I contenitori dei profili FSLogix archiviano un profilo utente completo in un singolo contenitore e sono progettati per eseguire il roaming dei profili in ambienti di elaborazione remota non persistenti come Windows Virtual Desktop. Quando si accede, il contenitore si connette dinamicamente all'ambiente di elaborazione utilizzando un disco rigido virtuale (VHD) supportato localmente e un disco rigido virtuale Hyper-V (VHDX). Queste tecnologie avanzate di filtro-driver consentono al profilo utente di essere immediatamente disponibile e vengono visualizzati nel sistema esattamente come un profilo utente locale. Per altre informazioni sui contenitori di profili FSLogix, vedere Contenitori di profili [FSLogix e file](fslogix-containers-azure-files.md)di Azure.

È possibile creare contenitori di profili FSLogix usando [File NetApp](https://azure.microsoft.com/services/netapp/)di Azure , un servizio di piattaforma nativo di Azure di facile utilizzo che consente ai clienti di eseguire rapidamente e in modo affidabile il provisioning dei volumi SMB di livello enterprise per gli ambienti Windows Virtual Desktop. Per altre informazioni sui file NetApp di Azure, vedere [Che cos'è](../azure-netapp-files/azure-netapp-files-introduction.md) file NetApp di Azure?

Questa guida illustra come configurare un account file NetApp di Azure e creare contenitori di profili FSLogix in Windows Virtual Desktop.

In questo articolo si presuppone che i [pool host](create-host-pools-azure-marketplace.md) siano già stati impostati e raggruppati in uno o più tenant nell'ambiente Windows Virtual Desktop. Per informazioni su come configurare i tenant, vedere [Creare un tenant in Desktop virtuale](tenant-setup-azure-active-directory.md) di Windows e il post di blog della community [tecnica](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Le istruzioni contenute in questa guida sono specifiche per gli utenti di Windows Virtual Desktop. Per istruzioni più generali su come configurare i file NetApp di Azure e creare contenitori di profili FSLogix all'esterno di Windows Virtual Desktop, vedere La guida introduttiva Configurazione dei file NetApp di [Azure e creare una guida introduttiva](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)del volume NFS .

>[!NOTE]
>Questo articolo non illustra le procedure consigliate per proteggere l'accesso alla condivisione File netApp di Azure.This article doesn't cover best practices for securing access to the Azure NetApp Files share.

>[!NOTE]
>Per informazioni sul confronto tra le diverse opzioni di archiviazione del contenitore di profili FSLogix in Azure, vedere Opzioni di archiviazione per i [contenitori di profili FSLogix.](store-fslogix-profile.md)

## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare un contenitore di profili FSLogix per un pool host, è necessario:Before you can create an FSLogix profile container for a host pool, you must:

- Configurare e configurare Windows Virtual Desktop
- Effettuare il provisioning di un pool host di Windows Virtual Desktop
- [Abilitare la sottoscrizione di File NetApp di AzureEnable your Azure NetApp Files subscription](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Configurare l'account file di Azure NetAppSet up your Azure NetApp Files account

Per iniziare, è necessario configurare un account File NetApp di Azure.To get started, you need to set up an Azure NetApp Files account.

1. Accedere al [portale](https://portal.azure.com)di Azure . Assicurati che il tuo account disponga delle autorizzazioni di collaboratore o amministratore.

2. Selezionare **l'icona di Azure Cloud Shell** a destra della barra di ricerca per aprire Azure Cloud Shell.

3. Quando Azure Cloud Shell è aperto, selezionare **PowerShell**.

4. Se è la prima volta che si usa Azure Cloud Shell, creare un account di archiviazione nella stessa sottoscrizione con cui si mantengono i file NetApp di Azure e il desktop virtuale di Windows.If this is your first time using Azure Cloud Shell, create a storage account in the same subscription you keep your Azure NetApp Files and Windows Virtual Desktop.

   ![Finestra dell'account di archiviazione con il pulsante Crea archiviazione nella parte inferiore della finestra evidenziata in rosso.](media/create-storage-button.png)

5. Dopo il caricamento di Azure Cloud Shell, eseguire i due cmdlet seguenti.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Sul lato sinistro della finestra selezionare **Tutti i servizi**. Immettere **File NetApp** di Azure nella casella di ricerca visualizzata nella parte superiore del menu.

   ![Schermata di un utente che immette "File NetApp di Azure" nella casella di ricerca Tutti i servizi. I risultati della ricerca mostrano la risorsa File NetApp di Azure.The search results show the Azure NetApp Files resource.](media/azure-netapp-files-search-box.png)


7. Selezionare **File NetApp** di Azure nei risultati della ricerca, quindi selezionare **Crea**.

8. Selezionare il pulsante **Aggiungi.Select** the Add button.
9. Quando si apre la scheda **Nuovo account NetApp,** immettere i seguenti valori:

    - In **Nome**immettere il nome dell'account NetApp.
    - Per **Sottoscrizione**, selezionare la sottoscrizione per l'account di archiviazione configurato nel passaggio 4 dal menu a discesa.
    - Per **Gruppo di**risorse , selezionare un gruppo di risorse esistente dal menu a discesa oppure crearne uno nuovo selezionando Crea **nuovo**.
    - In **Posizione**, selezionare l'area geografica per l'account NetApp dal menu a discesa. Questa area deve essere la stessa area delle macchine virtuali host della sessione.

   >[!NOTE]
   >File NetApp di Azure attualmente non supporta il montaggio di un volume tra aree.

10. Al termine, seleziona **Crea** per creare il tuo account NetApp.

## <a name="create-a-capacity-pool"></a>Creare un pool di capacitàCreate a capacity pool

Successivamente, creare un nuovo pool di capacità:Next, create a new capacity pool: 

1. Passare al menu File NetApp di Azure e selezionare il nuovo account.
2. Nel menu dell'account selezionare **Pool di capacità** in Servizio di archiviazione.
3. Selezionare **Aggiungi pool**.
4. Quando viene visualizzata la scheda **Nuovo pool di capacità,** immettere i valori seguenti:

    - In **Nome**immettere un nome per il nuovo pool di capacità.
    - Per **Livello di servizio**, selezionare il valore desiderato dal menu a discesa. Consigliamo **Premium** per la maggior parte degli ambienti.
       >[!NOTE]
       >L'impostazione Premium fornisce la velocità effettiva minima disponibile per un livello di servizio Premium, ovvero 256 MBps. Potrebbe essere necessario modificare questa velocità effettiva per un ambiente di produzione. La velocità effettiva finale si basa sulla relazione descritta in [Limiti di velocità effettiva.](../azure-netapp-files/azure-netapp-files-service-levels.md)
    - Per **Dimensioni (TiB)**, immettere le dimensioni del pool di capacità più adatte alle proprie esigenze. La dimensione minima è 4 TiB.

5. Al termine, fare clic su **OK**.

## <a name="join-an-active-directory-connection"></a>Partecipare a una connessione di Active Directory

Dopo di che, è necessario aggiungere una connessione di Active Directory.

1. Selezionare **Connessioni di Active Directory** nel menu sul lato sinistro della pagina, quindi selezionare il pulsante **Partecipa** per aprire la pagina Partecipa ad **Active Directory.**

   ![Schermata del menu Connessioni di Active Directory.](media/active-directory-connections-menu.png)

2. Immettere i valori seguenti nella pagina **Partecipa ad Active Directory** per accedere a una connessione:

    - Per **DNS primario**, immettere l'indirizzo IP del server DNS nell'ambiente in grado di risolvere il nome di dominio.
    - Per **Dominio**immettere il nome di dominio completo (FQDN).
    - Per **Prefisso server SMB (account computer),** immettere la stringa che si desidera aggiungere al nome dell'account computer.
    - In **Nome utente**immettere il nome dell'account con le autorizzazioni per eseguire l'aggiunta al dominio.
    - In **Password**immettere la password dell'account.

  >[!NOTE]
  >È consigliabile verificare che l'account computer creato in [Partecipa a una connessione](create-fslogix-profile-container.md#join-an-active-directory-connection) ad Active Directory sia presente nel controller di dominio in **Computer** o **nell'unità organizzativa pertinente dell'organizzazione.**

## <a name="create-a-new-volume"></a>Creare un nuovo volume

Successivamente, è necessario creare un nuovo volume.

1. Selezionare **Volumi**, quindi **Aggiungi volume**.

2. Quando si apre la scheda **Crea un volume,** immettere i seguenti valori:

    - In **Nome volume**immettere un nome per il nuovo volume.
    - Per **Pool di capacità**, selezionare il pool di capacità appena creato dal menu a discesa.
    - Per **Quota (GiB)** immettere la dimensione del volume appropriata per l'ambiente.
    - Per **Rete virtuale**, selezionare una rete virtuale esistente con connettività al controller di dominio dal menu a discesa.
    - In **Subnet**selezionare **Crea nuovo**. Tenere presente che questa subnet verrà delegata a File NetApp di Azure.Keep in mind that this subnet will be delegated to Azure NetApp Files.

3.  Selezionare **Avanti: Protocollo \> ** per aprire la scheda Protocollo e configurare i parametri di accesso al volume.

## <a name="configure-volume-access-parameters"></a>Configurare i parametri di accesso al volume

Dopo aver creato il volume, configurare i parametri di accesso al volume.

1.  Selezionare **SMB** come tipo di protocollo.
2.  In Configurazione nel menu a discesa **Active Directory** selezionare la stessa directory connessa in origine in Aggiunta a una connessione in [Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenere presente che esiste un limite di un Active Directory per ogni sottoscrizione.
3.  Nella casella di testo **Nome condivisione** immettere il nome della condivisione utilizzata dal pool host della sessione e dai relativi utenti.

4.  Nella parte inferiore della pagina, seleziona **Revisione e crea.** Verrà visualizzata la pagina di convalida. Dopo aver convalidato correttamente il volume, selezionare **Crea**.

5.  A questo punto, il nuovo volume inizierà a essere distribuito. Al termine della distribuzione, è possibile usare la condivisione File netApp di Azure.Once deployment is complete, you can use the Azure NetApp Files share.

6.  Per visualizzare il percorso di montaggio, selezionare **Vai alla risorsa** e cercarlo nella scheda Panoramica.

    ![Schermata della schermata Panoramica con una freccia rossa che punta al percorso di montaggio.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configurare FSLogix nelle macchine virtuali (VM) dell'host di sessioneConfigure FSLogix on session host virtual machines (VMs)

Questa sezione è basata su [Creare un contenitore di profili per un pool host utilizzando una condivisione file.](create-host-pools-user-profile.md)

1. [Scaricare il file .zip dell'agente FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) mentre si è ancora remoti nella macchina virtuale dell'host della sessione.

2. Decomprimere il file scaricato.

3. Nel file passare a **Versioni x64** > **Releases** ed eseguire **FSLogixAppsSetup.exe**. Si aprirà il menu di installazione.

4.  Se si dispone di un codice Product Key, immetterlo nella casella di testo Codice Product Key.

5. Selezionare la casella di controllo accanto a **Accetto le condizioni di licenza**.

6. Selezionare **Installa**.

7. Passare a **\\C:\\Programmi FSLogix\\Apps** per confermare l'agente installato.

8. Dal menu Start, eseguire **RegEdit** come amministratore.

9. Passare a **Software HKEY_LOCAL_MACHINE\\\\\\computer FSLogix**.

10. Creare una chiave denominata **Profili**.

11.  Creare un valore denominato **Enabled** con un **tipo di REG_DWORD** impostato su un valore di dati pari a **1**.

12. Creare un valore denominato **VHDLocations** con un tipo multistringa e impostarne il valore di dati sull'URI per la condivisione File di Rete di Azure.Create a value named VHDLocations with a **Multi-String** type and set its data value to the URI for the Azure NetApp Files share.

13. Creare un valore denominato **DeleteLocalProfileWhenVHDShouldApply** con un valore DWORD pari a 1 per evitare problemi con i profili locali esistenti prima di accedere.

     >[!WARNING]
     >Prestare attenzione quando si crea il valore DeleteLocalProfileWhenVHDShouldApply. Quando il sistema Profili FSLogix determina che un utente deve avere un profilo FSLogix, ma esiste già un profilo locale, Contenitore profili eliminerà definitivamente il profilo locale. L'utente verrà quindi eseguito l'accesso con il nuovo profilo FSLogix.

## <a name="assign-users-to-session-host"></a>Assegnare gli utenti all'host della sessioneAssign users to session host

1. Aprire **PowerShell ISE** come amministratore e accedere a Windows Virtual Desktop.

2. Eseguire i cmdlet seguenti:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando vengono richieste le credenziali, immettere le credenziali per l'utente con i ruoli Tenant Creator o Proprietario/Collaboratore RDS nel tenant di Windows Virtual Desktop.

4. Eseguire i cmdlet seguenti per assegnare un utente a un gruppo di Desktop remoto:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Assicurarsi che gli utenti possano accedere alla condivisione file di Azure NetApp

1. Aprire il browser Internet <https://rdweb.wvd.microsoft.com/webclient/index.html>e andare a .

2. Accedere con le credenziali di un utente assegnato al gruppo Desktop remoto.

3. Dopo aver stabilito la sessione utente, accedere al portale di Azure con un account amministrativo.

4. Aprire **File NetApp**di Azure, selezionare l'account File NetApp di Azure e quindi **selezionare Volumi**. Una volta aperto il menu Volumi, selezionare il volume corrispondente.

   ![Screenshot dell'account NetApp configurato in precedenza nel portale di Azure con il pulsante Volumi selezionato.](media/netapp-account.png)

5. Passare alla scheda **Panoramica** e verificare che il contenitore di profili FSLogix utilizzi lo spazio.

6. Connettersi direttamente a qualsiasi parte VM del pool host tramite Desktop remoto e aprire **Esplora file.** Passare quindi al **percorso di** montaggio (nell'esempio seguente, il percorso di montaggio \\ \\viene anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   All'interno di questa cartella, dovrebbe essere presente un profilo VHD (o VHDX) come quello nell'esempio seguente.

   ![Schermata del contenuto della cartella nel percorso di montaggio. All'interno è un singolo file VHD denominato "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile utilizzare i contenitori di profili FSLogix per configurare una condivisione profilo utente. Per informazioni su come creare condivisioni profilo utente con i nuovi contenitori, vedere Creare un contenitore di profili [per un pool host usando una condivisione file.](create-host-pools-user-profile.md)
