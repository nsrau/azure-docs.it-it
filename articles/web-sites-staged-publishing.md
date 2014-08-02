<properties  linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

# Distribuzione temporanea su Siti Web di Microsoft Azure

## Sommario

* [Panoramica](#Overview)
* [Per aggiungere uno slot di distribuzione a un sito Web](#Add)
* [Informazioni sulla configurazione degli slot di
  distribuzione](#AboutConfiguration)
* [Per scambiare gli slot di distribuzione](#Swap)
* [Per eseguire il rollback di un sito di produzione alla gestione
  temporanea](#Rollback)
* [Per eliminare uno slot di sito](#Delete)
* [Cmdlet di Azure PowerShell per gli slot di sito](#PowerShell)
* [Comandi dell'interfaccia della riga di comando multipiattaforma di
  Azure (xplat-cli) per gli slot di sito](#CLI)

<a name="Overview"></a>
## Panoramica ##
L'opzione che consente di creare slot di sito per i siti in modalità Standard in esecuzione su Siti Web di Microsoft Azure consente di abilitare un flusso di lavoro di distribuzione di gestione temporanea. È possibile creare uno slot di sito di sviluppo o di gestione temporanea per ciascun sito di produzione predefinito (che diventa uno slot di produzione) e scambiare questi slot senza tempi di inattività. La distribuzione di gestione temporanea si rivela particolarmente utile negli scenari seguenti:

* **Convalida prima della distribuzione**: dopo la distribuzione di contenuti o configurazione a uno slot di sito di gestione temporanea, è possibile convalidare le modifiche prima di applicarle al sito di produzione.

* **Creazione e integrazione di contenuti del sito**: è possibile aggiungere in modo incrementale gli aggiornamenti dei contenuti allo slot di distribuzione di gestione temporanea e poi applicare quest'ultimo al sito di produzione quando gli aggiornamenti saranno stati completati.

* **Rollback di un sito di produzione**: se il risultato delle modifiche applicate al sito di produzione non è quello previsto, è possibile ripristinare immediatamente i contenuti originali.

Microsoft Azure avvia a caldo tutte le istanze dello slot di sito di origine prima di applicarle al sito di produzione, eliminando gli avvii a freddo durante la distribuzione dei contenuti. Il reindirizzamento del traffico è lineare e nessuna richiesta viene eliminata in seguito alle operazioni di scambio. Attualmente per ogni sito web Standard è supportato un unico slot di distribuzione oltre a quello predefinito.

<a name="Add"></a>
## Per aggiungere uno slot di distribuzione a un sito Web ##

Il sito deve essere in esecuzione in modalità Standard per consentire la creazione di siti slot.

1.  Fare clic su **Add a new deployment slot** nella pagina Quick Start oppure nella sezione Quick Glance della pagina Dashboard del proprio sito Web.
    
    ![Add a new deployment
    slot](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)


   
    > [WACOM.NOTE]
    > Se il sito Web non è già in modalità Standard si riceverà il messaggio **You must be in the standard mode to enable staged publishing**. A questo punto è possibile selezionare **Upgrade** e passare alla scheda **Scale** del sito Web prima di continuare.

2.  Verrà visualizzata la finestra di dialogo **Add New Deployment
    Slot**.
    
    ![Finestra di dialogo Add New Deployment Slot](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)
    
    Specificare un nome per lo slot di distribuzione. Il nome non può superare i 60 caratteri alfanumerici. Non sono ammessi caratteri speciali o spazi.

3.  Nell'elenco dei siti Web espandere il segno a sinistra del nome del proprio sito Web per visualizzare lo slot di distribuzione, che avrà il nome del sito di produzione, seguito tra parentesi dal nome dello slot di distribuzione fornito dall'utente.
    
    ![Elenco dei siti con slot di distribuzione](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  Quando si seleziona il nome dello slot del sito di distribuzione si aprirà una pagina con un gruppo di schede, proprio come in qualsiasi altro sito Web. Nella parte superiore della pagina del portale sarà visualizzata la dicitura ***nome-sito-Web*(*nome-slot-di-distribuzione*)** per ricordare all'utente che sta visualizzando lo slot del sito di distribuzione.
    
    ![Titolo slot di distribuzione](./media/web-sites-staged-publishing/StagingTitle.png)

È ora possibile aggiornare contenuti e configurazione per lo slot del sito di distribuzione. Utilizzare le credenziali del profilo di pubblicazione o di distribuzione associate allo slot del sito di distribuzione per gli aggiornamenti dei contenuti.

<a name="AboutConfiguration"></a>
## Informazioni sulla configurazione degli slot di distribuzione ##
 Per impostazione predefinita, alla creazione di uno slot di distribuzione la relativa configurazione viene clonata dallo slot del sito di produzione. È possibile modificare la configurazione di tutti gli slot di sito.

**Elementi di configurazione che verranno modificati allo scambio degli
slot**:

* Impostazioni generali
* Stringhe di connessione
* Mapping dei gestori
* Impostazioni di monitoraggio e diagnostica

**Elementi di configurazione che non verranno modificati allo scambio
degli slot**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati e associazioni SSL
* Impostazioni di scalabilità

**Note**:

* Gli slot di distribuzione sono disponibili solo per i siti in modalità Standard.

* Se si modifica la modalità di un sito in Free, Shared o Basic, non sarà più possibile eseguire lo scambio.

* Se si intende applicare uno slot di distribuzione al sito di produzione è necessario configurarlo esattamente come dovrebbe essere nell'ambiente di produzione.

* Per impostazione predefinita, uno slot di distribuzione punterà allo stesso database del sito di produzione. Tuttavia, è possibile configurare lo slot di distribuzione in modo che punti a un database alternativo modificando la stringa o le stringhe di connessione al database per lo slot di distribuzione. Sarà quindi possibile ripristinare la stringa o le stringhe di connessione al database originali sullo slot di distribuzione poco prima di applicarlo al sito di produzione.

<a name="Swap"></a>
## Per scambiare gli slot di distribuzione ##

1.  Per scambiare gli slot di distribuzione, selezionare lo slot di distribuzione nell'elenco dei siti Web e fare clic sul pulsante **Swap** nella barra dei comandi.
    
    ![Pulsante Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  Viene visualizzata la finestra di dialogo Swap Deployments. La finestra di dialogo consente di scegliere quale slot di sito dovrebbe essere l'origine e quale sito dovrebbe essere la destinazione.
    
    ![Finestra di dialogo Swap Deployments](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  Fare clic sul segno di spunta per completare l'operazione. Al termine dell'operazione, lo scambio dei siti sarà stato eseguito correttamente.

<a name="Rollback"></a>
## Per eseguire il rollback di un sito di produzione alla gestione temporanea ##
Se vengono identificati eventuali errori di contenuti o configurazione applicati al sito di produzione, è possibile semplicemente scambiare uno slot di distribuzione (precedentemente il sito di produzione) riportandolo in ambiente di produzione e poi apportare ulteriori correzioni alla nuova versione del sito, mentre è in modalità di gestione temporanea.

> [WACOM.NOTE]
> Per la riuscita di un rollback lo slot di sito dovrà comunque contenere i contenuti e la configurazione non modificati del precedente sito di produzione.

<a name="Delete"></a>
## Per eliminare uno slot di sito ##

Fare clic su **Delete** nella barra dei comandi nella parte inferiore della pagina del portale Siti Web di Azure. Verrà offerta l'opzione di eliminare il sito Web e tutti gli slot di distribuzione oppure eliminare solo lo slot di distribuzione.

![Eliminare uno slot di
sito](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

Dopo aver risposto **Yes** al messaggio di conferma, uno o tutti gli slot verranno eliminati, a seconda dell'opzione selezionata.

**Note**:

* Gli slot di sito non di produzione non sono scalabili. Questa funzione è disponibile esclusivamente per gli slot di sito di produzione.

* La gestione delle risorse collegate non è supportata per gli slot di sito non di produzione.

* Se si desidera, è comunque possibile pubblicare direttamente sullo slot del sito di produzione.

* Attualmente, gli slot (siti) di distribuzione condividono le stesse risorse degli slot (siti) di produzione e vengono eseguiti sulle stesse macchine virtuali. Se si esegue il test di stress su uno slot di gestione temporanea, l'ambiente di produzione subirà un carico di stress analogo.

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## Cmdlet di Azure PowerShell per gli slot di sito

Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell, tra cui il supporto per la gestione degli slot di distribuzione di Siti Web di Azure.

* Per informazioni sull'installazione e la configurazione di Azure PowerShell e sull'autenticazione di Azure PowerShell con l'abbonamento di Windows Azure, vedere l'argomento relativo alla [procedura di installazione e configurazione di Azure PowerShell][1].

* Per elencare i cmdlet disponibili per Siti Web di Azure in PowerShell, chiamare `help AzureWebsite`.

* * *

### Get-AzureWebsite

Il cmdlet **Get-AzureWebsite** presenta informazioni su Siti Web di Azure per l'attuale sottoscrizione, come illustrato nell'esempio seguente.

`Get-AzureWebsite siteslotstest`

* * *

### New-AzureWebsite

È possibile creare un slot di sito per qualsiasi sito Web in modalità Standard utilizzando il cmdlet **New-AzureWebsite** e specificando i nomi del sito e dello slot. Indicare inoltre la stessa area del sito per la creazione dello slot di distribuzione, come nell'esempio seguente.

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * *

### Publish-AzureWebsiteProject

È possibile utilizzare il cmdlet **Publish-AzureWebsiteProject** per la distribuzione dei contenuti, come nell'esempio seguente.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

* * *

### Show-AzureWebsite

Dopo aver applicato gli aggiornamenti dei contenuti e della configurazione al nuovo slot, è possibile convalidarli passando allo slot che utilizza il cmdlet **Show-AzureWebsite**, come nell'esempio seguente.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * *

### Switch-AzureWebsiteSlot

Il cmdlet **Switch-AzureWebsiteSlot** può eseguire un'operazione di scambio per applicare lo slot di distribuzione aggiornato al sito di produzione, come nell'esempio seguente. Il sito di produzione non sarà caratterizzato da tempi di inattività né subirà un avvio a freddo.

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * *

### Remove-AzureWebsite

Se uno slot di distribuzione non è più necessario, è possibile eliminarlo utilizzando il cmdlet **Remove-AzureWebsite**, come nell'esempio seguente.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * *

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## Comandi dell'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) per gli slot di sito

L'interfaccia della riga di comando multipiattaforma (xplat-cli) di Azure fornisce comandi multipiattaforma per l'utilizzo con Azure, incluso il supporto per la gestione degli slot di distribuzione sui siti Web di Azure.

* Per istruzioni sull'installazione e la configurazione dell'interfaccia   xplat-cli, incluse le informazioni su come collegarla alla sottoscrizione di Azure, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][2].

* Per elencare i comandi disponibili per Siti Web di Azure nell'interfaccia xplat-cli, chiamare `azure site -h`.

* * *

### azure site list
Per informazioni sui siti Web di Azure nell'attuale sottoscrizione, chiamare **azure site list**, come nell'esempio seguente.

`azure site list siteslotstest`

* * *

### azure site create
Per creare uno slot di sito per qualsiasi sito Web in modalità Standard, chiamare **azure site create** e specificare il nome di un sito esistente e il nome dello slot da creare, come nell'esempio seguente.

`azure site create siteslotstest --slot staging`

Per abilitare il controllo del codice sorgente per il nuovo slot, utilizzare l'opzione **- git**, come nell'esempio seguente.

`azure site create –-git siteslotstest --slot staging`

* * *

### azure site swap
Per applicare lo slot di distribuzione aggiornato al sito di produzione, utilizzare il comando **azure site swap** per eseguire un'operazione di scambio, come nell'esempio seguente. Il sito di produzione non sarà caratterizzato da tempi di inattività né subirà un avvio a freddo.

`azure site swap siteslotstest`

* * *

### azure site delete Per
eliminare uno slot di distribuzione non più necessario, utilizzare il comando **azure site delete**, come nell'esempio seguente.

`azure site delete siteslotstest --slot staging`

* * *

<!-- IMAGES -->



[1]: http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell
[2]: http://www.windowsazure.com/en-us/documentation/articles/xplat-cli