---
title: Ridimensionare automaticamente gli host sessione desktop virtuale Windows-Azure
description: Viene descritto come configurare lo script di ridimensionamento automatico per gli host sessione desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: f0d847596ef21af67973b6572737e27e1d015991
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676495"
---
# <a name="automatically-scale-session-hosts"></a>Ridimensionare automaticamente gli host della sessione

Per molte distribuzioni di desktop virtuali Windows in Azure, i costi della macchina virtuale rappresentano una parte significativa del costo totale per la distribuzione di desktop virtuali Windows. Per ridurre i costi, è preferibile arrestare e deallocare le macchine virtuali (VM) host della sessione durante le ore di minore utilizzo, quindi riavviarle durante le ore di picco di utilizzo.

Questo articolo usa un semplice script di ridimensionamento per ridimensionare automaticamente le macchine virtuali host della sessione nell'ambiente di desktop virtuale Windows. Per ulteriori informazioni sul funzionamento dello script di ridimensionamento, vedere la sezione [come funziona il ridimensionamento dello script](#how-the-scaling-script-works) .

## <a name="prerequisites"></a>Prerequisiti

L'ambiente in cui si esegue lo script deve avere gli elementi seguenti:

- Un tenant e un account di desktop virtuale Windows o un'entità servizio con le autorizzazioni per eseguire query su tale tenant (ad esempio, collaboratore RDS).
- Macchine virtuali del pool host di sessione configurate e registrate con il servizio desktop virtuale di Windows.
- Una macchina virtuale aggiuntiva che esegue l'attività pianificata tramite Utilità di pianificazione e dispone dell'accesso di rete agli host di sessione. Questa operazione verrà definita più avanti nel documento come macchina virtuale scaler.
- Il [modulo Microsoft Azure Gestione risorse PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installato nella macchina virtuale che esegue l'attività pianificata.
- Il [modulo PowerShell per desktop virtuale di Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) installato nella macchina virtuale che esegue l'attività pianificata.

## <a name="recommendations-and-limitations"></a>Raccomandazioni e limitazioni

Quando si esegue lo script di ridimensionamento, tenere presenti le considerazioni seguenti:

- Questo script di ridimensionamento può gestire un solo pool di host per ogni istanza dell'attività pianificata che esegue lo script di ridimensionamento.
- Le attività pianificate che eseguono gli script di ridimensionamento devono trovarsi in una macchina virtuale sempre attiva.
- Creare una cartella separata per ogni istanza dello script di ridimensionamento e la relativa configurazione.
- Questo script non supporta l'accesso come amministratore al desktop virtuale di Windows con Azure AD account utente che richiedono l'autenticazione a più fattori. Si consiglia di utilizzare le entità servizio per accedere al servizio desktop virtuale di Windows e ad Azure. Seguire [questa esercitazione](create-service-principal-role-powershell.md) per creare un'entità servizio e un'assegnazione di ruolo con PowerShell.
- Il contratto di sicurezza di Azure si applica solo alle macchine virtuali in un set di disponibilità. La versione corrente del documento descrive un ambiente con una singola macchina virtuale che esegue il ridimensionamento, che potrebbe non soddisfare i requisiti di disponibilità.

## <a name="deploy-the-scaling-script"></a>Distribuire lo script di ridimensionamento

Nelle procedure riportate di seguito viene illustrato come distribuire lo script di ridimensionamento.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparare l'ambiente per lo script di ridimensionamento

Prima di tutto, preparare l'ambiente per lo script di ridimensionamento:

1. Accedere alla VM (scaler VM) che eseguirà l'attività pianificata con un account amministrativo di dominio.
2. Creare una cartella nella macchina virtuale scaler per mantenere lo script di ridimensionamento e la relativa configurazione (ad esempio, **C: \\scaling-HostPool1**).
3. Scaricare i file **basicScale. ps1**, **config. XML**e **Functions-PSStoredCredentials. ps1** e la cartella **PowershellModules** dal repository di [script di ridimensionamento](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copiarli nella cartella creata nel passaggio 2. Esistono due modi principali per ottenere i file prima di copiarli nella macchina virtuale scaler:
    - Clonare il repository git nel computer locale.
    - Visualizzare la versione non **elaborata** di ogni file, copiare e incollare il contenuto di ogni file in un editor di testo, quindi salvare i file con il nome file e il tipo di file corrispondenti. 

### <a name="create-securely-stored-credentials"></a>Creazione di credenziali archiviate in modo sicuro

Successivamente, è necessario creare le credenziali archiviate in modo sicuro:

1. Aprire PowerShell ISE come amministratore.
2. Importare il modulo di PowerShell per RDS eseguendo il cmdlet seguente:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Aprire il riquadro di modifica e caricare il file **Function-PSStoredCredentials. ps1** , quindi eseguire l'intero script (F5)
4. Eseguire il cmdlet seguente:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Ad esempio, **set-variable-name, percorso-valore globale "c: \\scaling-HostPool1"**
5. Eseguire il cmdlet **New-StoredCredential-GetPath \$KeyPath** . Quando richiesto, immettere le credenziali del desktop virtuale Windows con le autorizzazioni per eseguire una query sul pool host (il pool host è specificato nel **file config. XML**).
    - Se si usano entità servizio o account standard diversi, eseguire il cmdlet **New-StoredCredential-\$KeyPath** una volta per ogni account per creare le credenziali archiviate locali.
6. Eseguire **Get-StoredCredential-list** per verificare che le credenziali siano state create correttamente.

### <a name="configure-the-configxml-file"></a>Configurare il file config. XML

Immettere i valori appropriati nei campi seguenti per aggiornare le impostazioni dello script di ridimensionamento nel file config. XML:

| Campo                     | Descrizione                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | Azure AD ID tenant che associa la sottoscrizione in cui vengono eseguite le VM host sessione     |
| AADApplicationId              | ID applicazione dell'entità servizio                                                       |
| AADServicePrincipalSecret     | Questa operazione può essere specificata durante la fase di test, ma deve essere mantenuta vuota una volta create le credenziali con **Functions-PSStoredCredentials. ps1**    |
| currentAzureSubscriptionId    | ID della sottoscrizione di Azure in cui vengono eseguite le VM host della sessione                        |
| tenantName                    | Nome del tenant di desktop virtuale Windows                                                    |
| hostPoolName                  | Nome pool host per desktop virtuale Windows                                                 |
| RDBroker                      | URL per il servizio WVD, valore predefinito https: \//rdbroker. Wvd. Microsoft. com             |
| Nome utente                      | ID applicazione dell'entità servizio (è possibile avere la stessa entità servizio in AADApplicationId) o utente standard senza autenticazione a più fattori |
| isServicePrincipal            | I valori accettati sono **true** o **false**. Indica se il secondo set di credenziali utilizzato è un'entità servizio o un account standard. |
| BeginPeakTime                 | Quando inizia il tempo di utilizzo massimo                                                            |
| EndPeakTime                   | Al termine del tempo di utilizzo massimo                                                              |
| TimeDifferenceInHours         | Differenza di tempo tra l'ora locale e l'ora UTC, in ore                                   |
| SessionThresholdPerCPU        | Numero massimo di sessioni per soglia CPU usato per determinare quando è necessario avviare una nuova VM host sessione durante le ore di picco.  |
| MinimumNumberOfRDSH           | Numero minimo di macchine virtuali del pool host da rimanere in esecuzione durante i periodi di minore utilizzo             |
| LimitSecondsToForceLogOffUser | Numero di secondi di attesa prima di forzare la disconnessione degli utenti. Se impostato su 0, gli utenti non sono costretti a disconnettersi.  |
| LogOffMessageTitle            | Titolo del messaggio inviato a un utente prima che venga forzata la disconnessione                  |
| LogOffMessageBody             | Corpo del messaggio di avviso inviato agli utenti prima della disconnessione. Ad esempio, "il computer verrà arrestato in X minuti. Salvare il lavoro e disconnettersi ". |

### <a name="configure-the-task-scheduler"></a>Configurare la Utilità di pianificazione

Dopo aver configurato il file Configuration. XML, è necessario configurare il Utilità di pianificazione per eseguire il file basicScaler. ps1 a intervalli regolari.

1. Avviare **utilità di pianificazione**.
2. Nella finestra di **utilità di pianificazione** selezionare **Crea attività...**
3. Nella finestra di dialogo **Crea attività** selezionare la scheda **generale** , immettere un **nome** , ad esempio "Dynamic RDSH", selezionare **Esegui se l'utente è connesso o meno** ed **eseguire con privilegi più elevati**.
4. Passare alla scheda **trigger** , quindi selezionare **nuovo...**
5. Nella finestra di dialogo **nuovo trigger** , in **Impostazioni avanzate**, selezionare **Ripeti attività ogni** e selezionare il periodo e la durata appropriati (ad esempio, **15 minuti** o a **tempo indefinito**).
6. Selezionare la scheda **azioni** e **nuovo...**
7. Nella finestra di dialogo **nuova azione** immettere **PowerShell. exe** nel campo **programma/script** , quindi immettere **C: \\Scaling @ no__t-5basicScale. ps1** nel campo **Aggiungi argomenti (facoltativo)** .
8. Passare alle schede **condizioni** e **Impostazioni** e selezionare **OK** per accettare le impostazioni predefinite per ciascuna.
9. Immettere la password per l'account amministrativo in cui si prevede di eseguire lo script di ridimensionamento.

## <a name="how-the-scaling-script-works"></a>Funzionamento dello script di ridimensionamento

Questo script di ridimensionamento legge le impostazioni da un file config. XML, inclusi l'inizio e la fine del periodo di utilizzo massimo durante il giorno.

Durante i picchi di utilizzo, lo script controlla il numero corrente di sessioni e la capacità RDSH corrente per ogni pool host. Viene calcolato se le VM host della sessione in esecuzione dispongono di capacità sufficiente per supportare le sessioni esistenti in base al parametro SessionThresholdPerCPU definito nel file config. XML. In caso contrario, lo script avvia altre VM host sessione nel pool host.

Durante il tempo di utilizzo inferiore, lo script determina quali macchine virtuali host sessione devono essere arrestate in base al parametro MinimumNumberOfRDSH nel file config. XML. Lo script imposta le VM host della sessione sulla modalità di svuotamento per impedire che le nuove sessioni si connettano agli host. Se si imposta il parametro **LimitSecondsToForceLogOffUser** nel file config. XML su un valore positivo diverso da zero, lo script invierà una notifica a tutti gli utenti attualmente connessi per salvare il lavoro, attendere la quantità di tempo configurata e quindi forzare la disconnessione degli utenti. Una volta che tutte le sessioni utente sono state disposte in una macchina virtuale host sessione, lo script arresterà il server.

Se si imposta il parametro **LimitSecondsToForceLogOffUser** nel file config. XML su zero, lo script consentirà all'impostazione di configurazione della sessione nelle proprietà del pool host di gestire la firma delle sessioni utente. Se sono presenti sessioni in una macchina virtuale host sessione, la macchina virtuale host sessione verrà lasciata in esecuzione. Se non sono presenti sessioni, lo script arresterà la macchina virtuale host sessione.

Lo script è progettato per l'esecuzione periodica nel Server VM scaler usando Utilità di pianificazione. Selezionare l'intervallo di tempo appropriato in base alle dimensioni dell'ambiente di Servizi Desktop remoto e tenere presente che l'avvio e l'arresto delle macchine virtuali possono richiedere del tempo. Si consiglia di eseguire lo script di ridimensionamento ogni 15 minuti.

## <a name="log-files"></a>File di log

Lo script di ridimensionamento crea due file di log, **WVDTenantScale. log** e **WVDTenantUsage. log**. Il file **WVDTenantScale. log** registrerà gli eventi e gli errori, se presenti, durante ogni esecuzione dello script di ridimensionamento.

Il file **WVDTenantUsage. log** registrerà il numero attivo di core e il numero di macchine virtuali attivo ogni volta che si esegue lo script di ridimensionamento. È possibile usare queste informazioni per stimare l'utilizzo effettivo di Microsoft Azure macchine virtuali e i costi. Il file è formattato come valori delimitati da virgole, in cui ogni elemento contiene le informazioni seguenti:

>tempo, pool host, Core, VM

Il nome del file può anche essere modificato in modo da avere un'estensione CSV, caricata in Microsoft Excel e analizzata.
