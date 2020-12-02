---
title: Esercitazione per ordinare Azure Data Box | Microsoft Docs
description: Questa esercitazione fornisce informazioni su Azure Data Box, una soluzione ibrida che consente di importare dati locali in Azure, e illustra come ordinare il dispositivo Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: alkohli
ms.openlocfilehash: e1dca046177634842de25b255dd1bb22c5d2c5a5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964080"
---
# <a name="tutorial-order-azure-data-box"></a>Esercitazione: Ordinare Azure Data Box

Azure Data Box è una soluzione ibrida che consente di importare i dati locali in Azure in modo rapido, semplice e affidabile. I dati devono essere trasferiti in un dispositivo di archiviazione da 80 TB (capacità utilizzabile) fornito da Microsoft e quindi il dispositivo deve essere rispedito a Microsoft. Questi dati vengono poi caricati in Azure.

Questa esercitazione illustra come ordinare un dispositivo Azure Data Box. Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare un Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

## <a name="prerequisites"></a>Prerequisiti

# <a name="portal"></a>[Portale](#tab/portal)

Prima di distribuire il dispositivo, completare i prerequisiti di configurazione seguenti per il servizio e il dispositivo Data Box:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

È possibile accedere ad Azure ed eseguire i comandi dell'interfaccia della riga di comando di Azure in uno dei due modi seguenti:

* È possibile installare l'interfaccia della riga di comando ed eseguire i relativi comandi in locale.
* È possibile eseguire i comandi dell'interfaccia della riga di comando nel portale di Azure, in Azure Cloud Shell.

Per l'esercitazione verrà usata l'interfaccia della riga di comando di Azure tramite Windows PowerShell, ma è possibile scegliere una delle opzioni disponibili.

### <a name="for-azure-cli"></a>Per l'interfaccia della riga di comando di Azure

Prima di iniziare, verificare che:

#### <a name="install-the-cli-locally"></a>Installare l'interfaccia della riga di comando in locale

* Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli), versione 2.0.67 o successiva. In alternativa, è possibile eseguire l'[installazione tramite MSI](https://aka.ms/installazurecliwindows).

**Accedere ad Azure**

Aprire una finestra di comando di Windows PowerShell e accedere ad Azure con il comando [az login](/cli/azure/reference-index#az-login):

```azurecli
PS C:\Windows> az login
```

Di seguito è riportato l'output di un accesso riuscito:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Installare l'estensione dell'interfaccia della riga di comando per Azure Data Box**

Prima di poter usare i comandi dell'interfaccia della riga di comando per Azure Data Box, è necessario installare l'estensione. Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base. Per altre informazioni sulle estensioni, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

Per installare l'estensione per Azure Data Box, eseguire il comando seguente: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se l'installazione dell'estensione è riuscita, verrà visualizzato l'output seguente:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Usare Azure Cloud Shell

È possibile usare [Azure Cloud Shell](https://shell.azure.com/), un ambiente shell interattivo ospitato in Azure, tramite il browser per eseguire i comandi dell'interfaccia della riga di comando. Azure Cloud Shell supporta Bash o Windows PowerShell con i servizi di Azure. L'interfaccia della riga di comando di Azure è preinstallata e configurata per l'uso con l'account corrente. Selezionare il pulsante Cloud Shell nel menu nella sezione in alto a destra del portale di Azure:

![Selezione del menu Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

Il pulsante avvia una shell interattiva che è possibile usare per eseguire i passaggi riportati in questo articolo della guida pratica.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Per Azure PowerShell

Prima di iniziare, verificare di:

* Installare Windows PowerShell 6.2.4 o versione successiva.
* Installare il modulo di Azure PowerShell (AZ).
* Installare il modulo di Azure Data Box (Az.DataBox).
* Accedere ad Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Installare Azure PowerShell e i moduli in locale

**Installare o aggiornare Windows PowerShell**

Sarà necessario avere installato Windows PowerShell versione 6.2.4 o successiva. Per individuare la versione di PowerShell installata, eseguire `$PSVersionTable`.

Viene visualizzato l'output seguente:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Se la versione di Windows PowerShell è inferiore a 6.2.4, è necessario aggiornarla. Per installare la versione più recente di PowerShell, vedere [Installare Azure PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7&preserve-view=true).

**Installare i moduli di Azure PowerShell e Data Box**

Per usare Azure PowerShell e ordinare un dispositivo Azure Data Box, è necessario installare i moduli di Azure PowerShell. Per installare i moduli di Azure PowerShell:

1. Installare il [modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az).
2. Installare quindi Az.DataBox con il comando `Install-Module -Name Az.DataBox`.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire una finestra di comando di Windows PowerShell e accedere ad Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount):

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Di seguito è riportato l'output di un accesso riuscito:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Per informazioni dettagliate su come accedere ad Azure con Windows PowerShell, vedere [Accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Ordinare il Data Box

# <a name="portal"></a>[Portale](#tab/portal)

Seguire questa procedura nel portale di Azure per ordinare un dispositivo.

1. Usare le credenziali di Microsoft Azure per accedere all'URL [https://portal.azure.com](https://portal.azure.com).
2. Selezionare **+ Crea una risorsa** e cercare *Azure Data Box*. Selezionare **Azure Data Box**.

   ![Screenshot della sezione Nuovo con Azure Data Box nel campo di ricerca](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Selezionare **Crea**.

   ![Screenshot della sezione Azure Data Box con l'opzione Crea evidenziata](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Controllare se il servizio Data Box è disponibile nella propria area. Immettere o selezionare le informazioni seguenti e quindi selezionare **Applica**.

    |Impostazione  |valore  |
    |---------|---------|
    |Tipo di trasferimento     | Selezionare **Importa in Azure**.        |
    |Subscription     | Selezionare una sottoscrizione di tipo Contratto Enterprise, CSP o Azure Sponsorship per il servizio Data Box. <br> La sottoscrizione viene collegata all'account di fatturazione.       |
    |Gruppo di risorse | Selezionare un gruppo di risorse esistente. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. |
    |Paese/area geografica di origine    |    Selezionare il paese/area in cui si trovano attualmente i dati.         |
    |Area di Azure di destinazione     |     Selezionare l'area di Azure in cui si vogliono trasferire i dati. <br> Per altre informazioni, vedere [Disponibilità a livello di area](data-box-overview.md#region-availability).            |

    [![Avvio di un ordine di importazione di Azure Data Box](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Selezionare **Data Box**. La capacità massima che è possibile usare per un singolo ordine è di 80 TB. È possibile creare più ordini per volumi di dati maggiori.

    ![Dimensioni dei dati disponibili: Data Box Disk 40 terabyte, Data Box 100 terabyte, Data Box Heavy 1000 terabyte e Invio dei propri dischi 1 terabyte](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. In **Ordine** passare alla scheda **Informazioni di base**. Immettere o selezionare le informazioni seguenti e quindi selezionare **Avanti: Destinazione dei dati>** .

    |Impostazione  |valore  |
    |---------|---------|
    |Sottoscrizione      | La sottoscrizione viene popolata automaticamente in base alla selezione precedente.|
    |Resource group    | Il gruppo di risorse selezionato in precedenza. |
    |Nome ordine di importazione | Specificare un nome descrittivo per tenere traccia dell'ordine. <br> Il nome può contenere da 3 a 24 caratteri che possono essere lettere, numeri e trattini. <br> Il nome deve iniziare e terminare con una lettera o un numero.    |

    ![Importazione guidata ordine di Data Box, schermata Informazioni di base con le informazioni corrette compilate](media/data-box-deploy-ordered/select-data-box-import-06.png)<!--Generic subscription. Cut note. Box command.-->

7. Nella schermata **Destinazione dei dati** selezionare gli account di archiviazione o i dischi gestiti come **Destinazione dei dati**.

    Se si usano uno o più **account di archiviazione** come destinazione di archiviazione, viene visualizzata la schermata seguente:

    ![Importazione guidata ordine di Data Box, schermata Destinazione dati con gli account di archiviazione selezionati](media/data-box-deploy-ordered/select-data-box-import-07.png)

    In base all'area di Azure specificata, selezionare uno o più account di archiviazione nell'elenco filtrato di un account di archiviazione esistente. Il Data Box può essere collegato a un massimo di 10 account di archiviazione. È anche possibile creare un nuovo account **Utilizzo generico v1**, **Utilizzo generico v2** o un **account di archiviazione BLOB**.

    Sono supportati gli account di archiviazione con reti virtuali. Per consentire al servizio Data Box di lavorare con gli account di archiviazione protetti, abilitare i servizi attendibili all'interno delle impostazioni del firewall di rete dell'account di archiviazione. Per altre informazioni, vedere come [Aggiungere Azure Data Box come servizio attendibile](../storage/common/storage-network-security.md#exceptions).

    Se si usa Data Box per creare uno o più **dischi gestiti** dai dischi rigidi virtuali locali, sarà necessario fornire anche le informazioni seguenti:

    |Impostazione  |valore  |
    |---------|---------|
    |Gruppi di risorse     | Se si prevede la creazione di dischi gestiti da dischi rigidi virtuali in locale, creare nuovi gruppi di risorse. È possibile usare un gruppo di risorse esistente solo se è stato creato in precedenza durante la creazione di un ordine di Data Box per i dischi gestiti dal servizio Data Box. <br> Specificare più gruppi di risorse separati da punti e virgola. Vengono supportati un massimo di 10 gruppi di risorse.|

    ![Importazione guidata ordine di Data Box, schermata Destinazione dati con l'opzione Managed Disks selezionata](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    L'account di archiviazione specificato per i dischi gestiti viene usato come account di archiviazione di staging. Il servizio Data Box carica i dischi rigidi virtuali come BLOB di pagine per l'account di archiviazione di staging prima di convertirli in dischi gestiti e spostarli nei gruppi di risorse. Per altre informazioni, vedere [Verificare il caricamento dei dati in Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Se un BLOB di pagine non viene convertito correttamente in un disco gestito, rimane nell'account di archiviazione e l'archiviazione viene addebitata.

8. Selezionare **Avanti: Sicurezza** per continuare.

    La schermata **Sicurezza** consente di usare la propria chiave di crittografia e il proprio dispositivo e di condividere le password, nonché di scegliere la crittografia doppia.

    Tutte le impostazioni nella schermata **Sicurezza** sono facoltative. Se non si modifica alcuna impostazione, vengono applicate le impostazioni predefinite.

    ![Schermata sicurezza in Importazione guidata ordine di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Se si vuole usare la propria chiave gestita dal cliente per proteggere la passkey di sblocco per la nuova risorsa, espandere **Tipo di crittografia**.

    La configurazione di una chiave gestita dal cliente per Azure Data Box è facoltativa. Per impostazione predefinita, Data Box usa una chiave gestita da Microsoft per proteggere la passkey di sblocco.

    Una chiave gestita dal cliente non influisce sul modo in cui il dispositivo viene crittografato. Viene usata solo per crittografare la passkey di sblocco del dispositivo.

    Se non si vuole usare una chiave gestita dal cliente, andare al passaggio 15.

   ![Schermata Sicurezza che mostra le impostazioni di Tipo di crittografia](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Selezionare **Chiave gestita dal cliente** come tipo di chiave. Quindi selezionare **Seleziona un insieme di credenziali delle chiavi e la chiave**.
   
    ![Schermata Sicurezza, impostazioni per la chiave gestita dal cliente](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Nel pannello **Selezionare chiave da Azure Key Vault** viene automaticamente popolata la sottoscrizione.

    - Per **Insieme di credenziali delle chiavi** è possibile selezionare un insieme di credenziali delle chiavi esistente nell'elenco a discesa.

      ![Schermata Selezionare chiave da Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - È anche possibile selezionare **Crea nuovo** per creare un nuovo insieme di credenziali delle chiavi. Nella schermata **Crea insieme di credenziali delle chiavi** immettere il gruppo di risorse e il nome dell'insieme di credenziali delle chiavi. Assicurarsi che le opzioni **Eliminazione temporanea** e **Protezione dall'eliminazione** siano abilitate. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.

      ![Impostazioni per la creazione di un nuovo insieme di credenziali delle chiavi di Azure](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Esaminare le informazioni per l'insieme di credenziali delle chiavi e selezionare **Crea**. Attendere un paio di minuti mentre viene completata la creazione dell'insieme di credenziali delle chiavi.

      ![Schermata di revisione del nuovo insieme di credenziali delle chiavi di Azure](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. In **Selezionare chiave da Azure Key Vault** è possibile selezionare una chiave esistente nell'insieme di credenziali delle chiavi.

    ![Selezionare una chiave esistente da Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Se si vuole creare una nuova chiave, selezionare **Crea nuovo**. È necessario usare una chiave RSA. Le dimensioni possono essere maggiori o uguali a 2048. Immettere un nome per la nuova chiave, accettare le altre impostazioni predefinite e selezionare **Crea**.

      ![Opzione per creare una nuova chiave](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Dopo che la chiave viene creata nell'insieme di credenziali delle chiavi si riceve una notifica.

13. Selezionare un valore di **Versione** da usare per la chiave, quindi scegliere **Seleziona**.

      ![Nuova chiave creata nell'insieme di credenziali delle chiavi](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Se si vuole creare una nuova versione della chiave, selezionare **Crea nuovo**.

    ![Aprire una finestra di dialogo per creare una nuova versione della chiave](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Scegliere le impostazioni per la nuova versione della chiave, quindi selezionare **Crea**.

    ![Creare una nuova versione della chiave](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    Le impostazioni di **Tipo di crittografia** nella schermata **Sicurezza** mostrano l'insieme di credenziali delle chiavi e la chiave.

    ![Chiave e insieme di credenziali delle chiavi per una chiave gestita dal cliente](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Selezionare un'identità utente da usare per gestire l'accesso a questa risorsa. Scegliere **Select a user identity** (Selezionare un'identità utente). Nel pannello a destra selezionare la sottoscrizione e l'identità gestita da usare. Quindi scegliere **Seleziona**.

    Un'identità gestita assegnata dall'utente è una risorsa di Azure autonoma che è possibile usare per gestire più risorse. Per altre informazioni, vedere [Tipi di identità gestita](/azure/active-directory/managed-identities-azure-resources/overview).  

    Se è necessario creare una nuova identità gestita, seguire le istruzioni riportate in [Creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente mediante il portale di Azure](/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal).
    
    ![Selezionare un'identità utente](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    L'identità utente viene visualizzata nelle impostazioni di **Tipo di crittografia**.

    ![Un'identità utente selezionata mostrata nelle impostazioni di Tipo di crittografia](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Se non si vogliono usare le password generate dal sistema usate da Azure Data Box per impostazione predefinita, espandere **Bring your own password** (Usa la password personalizzata) nella schermata **Sicurezza**.

    Le password generate dal sistema sono sicure e sono consigliate, a meno che l'organizzazione non abbia altri requisiti.

    ![Opzioni di Bring your own password (Usa la password personalizzata) per un ordine di importazione di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Per usare la propria password per il nuovo dispositivo, per **Set preference for the device password** (Imposta preferenza per la password del dispositivo) selezionare **Use your own password** (Usa password personale) e digitare una password che soddisfi i requisiti di sicurezza.
   
     ![Opzioni per l'uso di una password del dispositivo personalizzata nella schermata Sicurezza per un ordine di importazione di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Per usare le proprie password per le condivisioni:

   - Per **Set preference for share passwords** (Imposta preferenze per le password delle condivisioni) selezionare **Use your own passwords** (Usa password personali) e quindi **Select passwords for the shares** (Seleziona password per le condivisioni).
     
        ![Opzioni per l'uso di una password personalizzata per la condivisione nella schermata Sicurezza per un ordine di importazione di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    - Digitare una password per ogni account di archiviazione incluso nell'ordine. La password verrà usata in tutte le condivisioni per l'account di archiviazione.
     
        Per usare la stessa password per tutti gli account di archiviazione, selezionare **Copia in tutte**. Al termine, selezionare **Salva**.
     
        ![Schermata per l'immissione delle password per le condivisioni per un ordine di importazione di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

       Nella schermata **Sicurezza** è possibile usare **View or change passwords** (Visualizza o cambia password) per cambiare le password.

16. In **Sicurezza**, se si vuole abilitare la crittografia doppia basata su software, espandere **Double-encryption (for highly secure environments)** (Crittografia doppia per ambienti altamente sicuri) e selezionare **Abilita crittografia doppia per l'ordine**.

    ![Schermata Sicurezza per l'importazione di Data Box con l'opzione per abilitare la crittografia basata su software per un ordine di Data Box](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    La crittografia basata su software viene eseguita in aggiunta alla crittografia AES a 256 bit dei dati nel Data Box.

    > [!NOTE]
    > Se si abilita questa opzione, l'elaborazione dll'ordine e la copia dei dati potrebbero richiedere più tempo. Non è possibile modificare questa opzione dopo aver creato l'ordine.

    Selezionare **Avanti: Dettagli contatto** per continuare.

17. In **Dettagli contatto** selezionare **+ Aggiungi indirizzo di spedizione**.

    ![Dalla schermata Dettagli contatto aggiungere gli indirizzi di spedizione all'ordine di importazione Azure Data Box](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. In **Indirizzo di spedizione** specificare nome e cognome, nome e indirizzo postale della società e un numero di telefono valido. Selezionare **Convalida indirizzo**. Il servizio convalida l'indirizzo di spedizione per la disponibilità del servizio. Se il servizio è disponibile per l'indirizzo di spedizione specificato, si riceve una notifica in tal senso.

    ![Screenshot della finestra di dialogo per l'aggiunta dell'indirizzo di spedizione, con le opzioni di spedizione e il pulsante per l'aggiunta dell'indirizzo evidenziati.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Se è stata selezionata l'opzione di spedizione autogestita, al completamento dell'ordine si riceverà una notifica tramite posta elettronica. Per altre informazioni sulla spedizione autogestita, vedere [Usare la spedizione autogestita](data-box-portal-customer-managed-shipping.md).

19. Dopo la convalida riuscita dei dettagli della spedizione, selezionare **Aggiungi indirizzo di spedizione**. Si tornerà nella scheda **Dettagli contatto**.

20. Nella scheda **Dettagli contatto** aggiungere uno o più indirizzi di posta elettronica. Il servizio invia notifiche tramite posta elettronica per qualsiasi aggiornamento dello stato dell'ordine agli indirizzi di posta elettronica specificati.

    È consigliabile usare un indirizzo di posta elettronica di gruppo in modo da continuare a ricevere le notifiche anche se un amministratore del gruppo non è disponibile.

    ![Sezione Posta elettronica dei Dettagli contatto nella procedura guidata per l'ordine](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. In **Rivedi e ordina** rivedere le informazioni relative a ordine, contatto, notifica e informativa sulla privacy. Selezionare la casella corrispondente per accettare le condizioni per la privacy.

22. Selezionare **Ordina**. Per la creazione dell'ordine sono richiesti pochi minuti.

    ![Schermata Rivedi e ordina della procedura guidata per l'ordine](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Seguire questa procedura nell'interfaccia della riga di comando di Azure per ordinare un dispositivo:

1. Prendere nota delle impostazioni per l'ordine di Data Box. Queste impostazioni includono le informazioni personali/aziendali, il nome della sottoscrizione, le informazioni sul dispositivo e le informazioni sulla spedizione. Sarà necessario usare queste impostazioni come parametri quando si esegue il comando dell'interfaccia della riga di comando per creare l'ordine di Data Box. Nella tabella seguente vengono illustrate le impostazioni dei parametri usate per `az databox job create`:

   | Impostazione (parametro) | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group| Usare un gruppo esistente o crearne uno nuovo. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name| Nome dell'ordine che si sta creando. | "mydataboxorder"|
   |contact-name| Nome associato all'indirizzo di spedizione. | "Gus Poland"|
   |phone| Numero di telefono della persona o dell'azienda che riceverà l'ordine.| "14255551234"
   |posizione| Area di Azure più vicina all'utente da cui verrà spedito il dispositivo.| "Stati Uniti occidentali"|
   |sku| Dispositivo Data Box specifico che si sta ordinando. I valori validi sono: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |email-list| Indirizzi di posta elettronica associati all'ordine.| "gusp@contoso.com" |
   |street-address1| Indirizzo a cui verrà spedito l'ordine. | "15700 NE 39th St" |
   |street-address2| Informazioni secondarie relative all'indirizzo, ad esempio numero di appartamento o numero di edificio. | "Bld 123" |
   |city| Città a cui verrà spedito il dispositivo. | "Redmond" |
   |state-or-province| Stato a cui verrà spedito il dispositivo.| "WA" |
   |country| Paese a cui verrà spedito il dispositivo. | "Stati Uniti" |
   |postal-code| Codice postale associato all'indirizzo di spedizione.| "98052"|
   |company-name| Nome dell'azienda per cui l'utente lavora.| "Contoso, LTD" |
   |archiviazione di Azure| Account di archiviazione di Azure da cui verranno importati i dati.| "mystorageaccount"|
   |debug| Include le informazioni di debug per la registrazione dettagliata  | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

2. Nel prompt dei comandi preferito o nel terminale eseguire il comando [az data box job create](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create&preserve-view=true) per creare l'ordine di Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Ecco un esempio di sintassi del comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Per impostazione predefinita, tutti i comandi dell'interfaccia della riga di comando di Azure useranno JSON come formato di output, a meno che non lo si modifichi. È possibile modificare il formato di output usando il parametro globale `--output <output-format>`. La modifica del formato in "table" migliorerà la leggibilità dell'output.

   Ecco lo stesso comando appena eseguito con una piccola modifica per cambiare la formattazione:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Seguire questa procedura usando Azure PowerShell per ordinare un dispositivo:

1. Prima di creare l'ordine di importazione, è necessario ottenere l'account di archiviazione e salvare l'oggetto account di archiviazione in una variabile.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Prendere nota delle impostazioni per l'ordine di Data Box. Queste impostazioni includono le informazioni personali/aziendali, il nome della sottoscrizione, le informazioni sul dispositivo e le informazioni sulla spedizione. Sarà necessario usare queste impostazioni come parametri quando si esegue il comando di PowerShell per creare l'ordine di Data Box. La tabella seguente mostra le impostazioni dei parametri usati per [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Impostazione (parametro) | Descrizione |  Valore di esempio |
    |---|---|---|
    |ResourceGroupName [obbligatorio]| Usare un gruppo di risorse esistente. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
    |Name [obbligatorio]| Nome dell'ordine che si sta creando. | "mydataboxorder"|
    |ContactName [obbligatorio]| Nome associato all'indirizzo di spedizione. | "Gus Poland"|
    |PhoneNumber [obbligatorio]| Numero di telefono della persona o dell'azienda che riceverà l'ordine.| "14255551234"
    |Location [obbligatorio]| Area di Azure più vicina all'utente da cui verrà spedito il dispositivo.| "WestUS"|
    |DataBoxType [obbligatorio]| Dispositivo Data Box specifico che si sta ordinando. I valori validi sono: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
    |EmailId [obbligatorio]| Indirizzi di posta elettronica associati all'ordine.| "gusp@contoso.com" |
    |StreetAddress1 [obbligatorio]| Indirizzo a cui verrà spedito l'ordine. | "15700 NE 39th St" |
    |StreetAddress2| Informazioni secondarie relative all'indirizzo, ad esempio numero di appartamento o numero di edificio. | "Bld 123" |
    |StreetAddress3| Informazioni sul terzo indirizzo. | |
    |City [obbligatorio]| Città a cui verrà spedito il dispositivo. | "Redmond" |
    |StateOrProvinceCode [obbligatorio]| Stato a cui verrà spedito il dispositivo.| "WA" |
    |CountryCode [obbligatorio]| Paese a cui verrà spedito il dispositivo. | "Stati Uniti" |
    |PostalCode [obbligatorio]| Codice postale associato all'indirizzo di spedizione.| "98052"|
    |CompanyName| Nome dell'azienda per cui l'utente lavora.| "Contoso, LTD" |
    |StorageAccountResourceId [obbligatorio]| L'ID dell'account di archiviazione di Azure da cui verranno importati i dati.| <AzStorageAccount>.id |

3. Nel prompt dei comandi preferito o nel terminale usare il comando [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) per creare l'ordine di Azure Data Box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Monitorare l'ordine

# <a name="portal"></a>[Portale](#tab/portal)

Dopo aver inserito l'ordine, è possibile monitorare lo stato dell'ordine dal portale di Azure. Passare all'ordine del Data Box e quindi a **Panoramica** per visualizzare lo stato. Il portale mostra l'ordine con stato **Ordinato**.

Se il dispositivo non è disponibile, si riceverà una notifica. Se il dispositivo è disponibile, Microsoft identifica il dispositivo per la spedizione e lo prepara. Durante la preparazione del dispositivo vengono eseguite le azioni seguenti:

* Vengono create condivisioni SMB per ogni account di archiviazione associato al dispositivo.
* Per ogni condivisione vengono generate le credenziali di accesso, come nome utente e password.
* Viene generata anche una password per il dispositivo che consente di sbloccare il dispositivo.
* Il Data Box viene bloccato per impedire qualsiasi accesso non autorizzato al dispositivo.

Una volta completata la preparazione del dispositivo, lo stato dell'ordine nel portale diventa **Elaborato**.

![Ordine di Data Box elaborato](media/data-box-overview/data-box-order-status-processed.png)

Microsoft prepara e spedisce quindi il dispositivo tramite un corriere locale. Si riceverà un numero di tracciabilità dopo la spedizione del dispositivo. Il portale mostra l'ordine con lo stato **Spedito**.

![Ordine di Data Box spedito](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Monitorare un singolo ordine

Per ottenere le informazioni di tracciabilità su un singolo ordine di Azure Data Box esistente, eseguire [az databox job show](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show&preserve-view=true). Il comando visualizza le informazioni sull'ordine, ad esempio: nome, gruppo di risorse, informazioni di tracciabilità, ID sottoscrizione, informazioni di contatto, tipo di spedizione e SKU del dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job show`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da visualizzare. | "mydataboxorder"|
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Ecco un esempio del comando con il formato di output impostato su "tabella":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> L'elenco degli ordini può essere supportato a livello di sottoscrizione e, in tal caso, il gruppo di risorse diventa un parametro facoltativo, anziché un parametro obbligatorio.

### <a name="list-all-orders"></a>Elencare tutti gli ordini

Se sono stati ordinati più dispositivi, è possibile eseguire [az databox job list](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list&preserve-view=true) per visualizzare tutti gli ordini di Azure Data Box. Il comando elenca tutti gli ordini che appartengono a un gruppo di risorse specifico. Nell'output sono visualizzate anche le informazioni seguenti: nome dell'ordine, stato di spedizione, area di Azure, tipo di recapito, stato dell'ordine. Nell'elenco sono anche inclusi gli ordini annullati.
Il comando visualizza inoltre i timestamp di ogni ordine.

```azurecli
az databox job list --resource-group <resource-group>
```

Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job list`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse contenente l'ordine. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Ecco un esempio del comando con il formato di output impostato su "tabella":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Monitorare un singolo ordine

Per ottenere le informazioni di tracciabilità su un singolo ordine di Azure Data Box esistente, eseguire [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). Il comando visualizza le informazioni sull'ordine, ad esempio: nome, gruppo di risorse, informazioni di tracciabilità, ID sottoscrizione, informazioni di contatto, tipo di spedizione e SKU del dispositivo.

> [!NOTE]
> `Get-AzDataBoxJob` si usa per visualizzare sia ordini singoli che multipli. La differenza è che per gli ordini singoli si specifica il nome dell'ordine.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `Get-AzDataBoxJob`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |ResourceGroup [obbligatorio]| Nome del gruppo di risorse associato all'ordine. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |Name [obbligatorio]| Il nome dell'ordine per cui ottenere informazioni. | "mydataboxorder"|
   |ResourceId| L'ID della risorsa associata all'ordine. |  |

   Ecco un esempio del comando con l'output:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Elencare tutti gli ordini

Se sono stati ordinati più dispositivi, è possibile eseguire [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob) per visualizzare tutti gli ordini di Azure Data Box. Il comando elenca tutti gli ordini che appartengono a un gruppo di risorse specifico. Nell'output sono visualizzate anche le informazioni seguenti: nome dell'ordine, stato di spedizione, area di Azure, tipo di recapito, stato dell'ordine. Nell'elenco sono anche inclusi gli ordini annullati.
Il comando visualizza inoltre i timestamp di ogni ordine.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Ecco un esempio del comando:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Annullare l'ordine

# <a name="portal"></a>[Portale](#tab/portal)

Per annullare l'ordine, nel portale di Azure passare a **Panoramica** e fare clic su **Annulla** sulla barra dei comandi.

Dopo aver effettuato un ordine, è possibile annullarlo in qualsiasi momento prima che il suo stato venga contrassegnato come elaborato.

Per eliminare un ordine annullato, passare a **Panoramica** e fare clic su **Elimina** sulla barra dei comandi.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Annullare un ordine

Per annullare un ordine di Azure Data Box, eseguire [az databox job cancel](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel&preserve-view=true). È necessario specificare il motivo per l'annullamento dell'ordine.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job cancel`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine da eliminare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
   |reason [obbligatorio]| Motivo per l'annullamento dell'ordine. | "Ho immesso informazioni errate e ho dovuto annullare l'ordine." |
   |sì| Indica che non è richiesta la conferma. | --yes (-y)| --yes -y |
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

   Ecco un esempio del comando con l'output:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Eliminare un ordine

Se è stato annullato un ordine di Azure Data Box, è possibile eseguire [az databox job delete](/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete&preserve-view=true) per eliminare l'ordine.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   Nella tabella seguente vengono illustrate le informazioni sui parametri per `az databox job delete`:

   | Parametro | Descrizione |  Valore di esempio |
   |---|---|---|
   |resource-group [obbligatorio]| Nome del gruppo di risorse associato all'ordine da eliminare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
   |name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
   |sottoscrizione| Nome o ID (GUID) della propria sottoscrizione di Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |sì| Indica che non è richiesta la conferma. | --yes (-y)| --yes -y |
   |debug| Include le informazioni di debug per la registrazione dettagliata | --debug |
   |help| Visualizza le informazioni della Guida per questo comando. | --help -h |
   |only-show-errors| Mostra solo gli errori, eliminando gli avvisi. | --only-show-errors |
   |output -o| Imposta il formato di output.  Valori consentiti: json, jsonc, none, table, tsv, yaml, yamlc. Il valore predefinito è json. | --output "json" |
   |query| Stringa di query JMESPath. Per altre informazioni, vedere [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Include la registrazione dettagliata. | --verbose |

Ecco un esempio del comando con l'output:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Annullare un ordine

Per annullare un ordine di Azure Data Box, eseguire [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). È necessario specificare il motivo per l'annullamento dell'ordine.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

Nella tabella seguente vengono illustrate le informazioni sui parametri per `Stop-AzDataBoxJob`:

| Parametro | Descrizione |  Valore di esempio |
|---|---|---|
|ResourceGroup [obbligatorio]| Il nome del gruppo di risorse associato all'ordine da annullare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
|Name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
|Reason [obbligatorio]| Motivo per l'annullamento dell'ordine. | "Ho immesso informazioni errate e ho dovuto annullare l'ordine." |
|Force | Forza l'esecuzione del cmdlet senza conferma dell'utente. | -Force |

Ecco un esempio del comando con l'output:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Eliminare un ordine

Se è stato annullato un ordine di Azure Data Box, è possibile eseguire [Remove-AzDataBoxJob](/powershell/module/az.databox/remove-azdataboxjob) per eliminarlo.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

Nella tabella seguente vengono illustrate le informazioni sui parametri per `Remove-AzDataBoxJob`:

| Parametro | Descrizione |  Valore di esempio |
|---|---|---|
|ResourceGroup [obbligatorio]| Nome del gruppo di risorse associato all'ordine da eliminare. Un gruppo di risorse è un contenitore logico per le risorse che possono essere gestite o distribuite insieme. | "myresourcegroup"|
|Name [obbligatorio]| Nome dell'ordine da eliminare. | "mydataboxorder"|
|Force | Forza l'esecuzione del cmdlet senza conferma dell'utente. | -Force |

Ecco un esempio del comando con l'output:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Di seguito è riportato l'output ottenuto dall'esecuzione del comando:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad articoli di Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti per la distribuzione del Data Box
> * Ordinare il Data Box
> * Monitorare l'ordine
> * Annullare l'ordine

Passare all'esercitazione successiva per informazioni su come configurare il Data Box.

> [!div class="nextstepaction"]
> [Configurare Azure Data Box](./data-box-deploy-set-up.md)
