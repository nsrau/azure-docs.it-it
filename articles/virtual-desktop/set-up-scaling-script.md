---
title: Ridimensionare automaticamente gli host della sessione di anteprima di Desktop virtuale Windows - Azure
description: Viene descritto come configurare lo script di scalabilità automatica per gli host della sessione di Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: e9f500e3ab965b9dbfc5e395a6572497c85f6f8f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755140"
---
# <a name="automatically-scale-session-hosts"></a>Ridimensionare automaticamente gli host della sessione

Per molte distribuzioni di anteprima di Desktop virtuale Windows in Azure, i costi di macchine virtuali rappresentano una parte significativa del costo totale distribuzione Desktop virtuale Windows. Per ridurre i costi, è consigliabile arrestare e deallocare sessione ospitare macchine virtuali (VM) durante le ore non di punta, quindi riavviarle durante le ore di punta.

Questo articolo usa un semplice script di ridimensionamento per ridimensionare automaticamente macchine virtuali host sessione Desktop virtuale Windows nell'ambiente in uso. Per altre informazioni su come funziona lo script di ridimensionamento, vedere la [come funziona lo script scala](#how-the-scaling-script-works) sezione.

## <a name="prerequisites"></a>Prerequisiti

L'ambiente in cui si esegue lo script deve avere le operazioni seguenti:

- Un tenant di Desktop virtuale Windows e account o un'entità servizio con le autorizzazioni per eseguire query sui tenant (ad esempio collaboratore di servizi desktop remoto).
- Macchine virtuali del pool host sessione configurato e registrato con il servizio di Windows Desktop virtuale.
- Un'altra macchina virtuale che esegue l'attività pianificata tramite utilità di pianificazione e ha accesso alla rete agli host della sessione. Questo verrà essere indicato più avanti nel documento come scaler della macchina virtuale.
- Il [modulo PowerShell di Resource Manager di Microsoft Azure](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) installato nella macchina virtuale che esegue l'attività pianificata.
- Il [modulo di Windows PowerShell di Desktop virtuale](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) installato nella macchina virtuale che esegue l'attività pianificata.

## <a name="recommendations-and-limitations"></a>Indicazioni e limitazioni

Quando si esegue lo script scala, tenere presente quanto segue:

- Questo script di ridimensionamento può gestire solo un pool di host per ogni istanza dell'attività pianificata che esegue lo script di ridimensionamento.
- Le attività pianificate che eseguono gli script di ridimensionamento devono trovarsi in una macchina virtuale che è sempre attiva.
- Creare una cartella separata per ogni istanza di ridimensionamento script e la relativa configurazione.
- Questo script non supporta l'accesso come amministratore di desktop virtuale Windows con account utente di Azure AD che richiedono l'autenticazione a più fattori. È consigliabile che usare le entità servizio per accedere al servizio di Desktop virtuale Windows e Azure. Seguire [in questa esercitazione](create-service-principal-role-powershell.md) per creare un'entità servizio e un'assegnazione di ruolo con PowerShell.
- Garanzia di contratto di servizio di Azure si applica solo alle macchine virtuali nel set di disponibilità. La versione corrente del documento viene descritto un ambiente con una singola macchina virtuale esegue il ridimensionamento, che potrebbe non soddisfare i requisiti di disponibilità.

## <a name="deploy-the-scaling-script"></a>Distribuire lo script di ridimensionamento

Le procedure seguenti illustreranno come distribuire lo script di ridimensionamento.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparare l'ambiente per lo script di ridimensionamento

Prima di tutto, preparare l'ambiente per lo script scala:

1. Accedere alla macchina virtuale (VM scaler) che eseguirà l'attività pianificata con un account amministrativo di dominio.
2. Creare una cartella nella macchina virtuale per contenere lo script di ridimensionamento e la relativa configurazione scaler (ad esempio, **c:\\HostPool1 ridimensionamento**).
3. Scaricare il **basicScale.ps1**, **config. XML**, e **funzioni PSStoredCredentials.ps1** file e il **PowershellModules** cartella dal [ridimensionamento nello script repository](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copiarli nella cartella creata nel passaggio 2. Esistono due modi principali per ottenere i file prima di copiarli per la macchina virtuale di scaler:
    - Clonare il repository git nel computer locale.
    - Visualizza i **Raw** versione di ogni file, copiare e incollare il contenuto di ogni file in un editor di testo, quindi salvare i file con il nome del file corrispondente e il tipo di file. 

### <a name="create-securely-stored-credentials"></a>Creare le credenziali archiviate in modo sicuro

Successivamente, è necessario creare le credenziali archiviate in modo sicuro:

1. Aprire PowerShell ISE come amministratore.
2. Importare il modulo PowerShell di servizi desktop remoto eseguendo il cmdlet seguente:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Aprire il riquadro di modifica e caricare il **funzione PSStoredCredentials.ps1** file.
4. Eseguire il cmdlet seguente:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Ad esempio, **Set-Variable - Name KeyPath-ambito globale-valore "c:\\HostPool1 scalabilità"**
5. Eseguire la **New-StoredCredential - KeyPath \$KeyPath** cmdlet. Quando richiesto, immettere le credenziali di Desktop virtuale Windows con le autorizzazioni per eseguire una query il pool di host (il pool di host è specificato nella **config. XML**).
    - Se si usa diverse entità servizio o account standard, eseguire la **New-StoredCredential - KeyPath \$KeyPath** cmdlet una volta per ogni account creare locale le credenziali archiviate.
6. Eseguire **Get-StoredCredentials-elenco** per confermare le credenziali sono state create correttamente.

### <a name="configure-the-configxml-file"></a>Configurare il file config. Xml

Immettere i valori appropriati nei campi seguenti per aggiornare le impostazioni di scalabilità script nel file config. XML:

| Campo                     | Descrizione                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID del AD Tenant Azure che consente di associare la sottoscrizione in cui la sessione host macchine virtuali in esecuzione     |
| AADApplicationId              | ID applicazione dell'entità servizio                                                       |
| AADServicePrincipalSecret     | Questo può essere immesso durante la fase di test, ma devono essere mantenuti vuoto dopo aver creato le credenziali con **funzioni PSStoredCredentials.ps1**    |
| currentAzureSubscriptionId    | Eseguire l'ID della sottoscrizione di Azure in cui la sessione di ospitare le macchine virtuali                        |
| tenantName                    | Nome del tenant di Desktop virtuale Windows                                                    |
| hostPoolName                  | Nome del pool di Desktop virtuale di Windows host                                                 |
| RDBroker                      | URL del servizio, WVD predefinito valore https:\//rdbroker.wvd.microsoft.com             |
| Username                      | L'ID applicazione di un'entità servizio (è possibile che la stessa entità servizio come AADApplicationId) o un utente standard senza multi-factor authentication |
| isServicePrincipal            | Valori accettati sono **true** oppure **false**. Indica se il secondo set di credenziali in uso è un'entità servizio o un account standard. |
| BeginPeakTime                 | Quando inizia la fase di picco dell'utilizzo                                                            |
| EndPeakTime                   | Quando termina il tempo di utilizzo di picco                                                              |
| TimeDifferenceInHours         | Differenza tra ora locale e l'ora UTC, in ore                                   |
| SessionThresholdPerCPU        | Numero massimo di sessioni per ogni valore soglia della CPU utilizzato per determinare quando una macchina virtuale host nuova sessione deve essere avviato durante le ore di picco.  |
| MinimumNumberOfRDSH           | Numero minimo di pool di host macchine virtuali di rimanere in esecuzione durante la fase di utilizzo non di punta             |
| LimitSecondsToForceLogOffUser | Numero di secondi di attesa prima di forzare disconnessione degli utenti. Se impostato su 0, gli utenti non è costretti a disconnettersi.  |
| LogOffMessageTitle            | Titolo del messaggio inviato a un utente prima che si è costretti a disconnettersi                  |
| LogOffMessageBody             | Corpo del messaggio di avviso inviato agli utenti prima che si è disconnessi. Ad esempio, "arresterà la macchina verso il basso di X minuti. Salvare il lavoro e disconnettersi." |

### <a name="configure-the-task-scheduler"></a>Configurare l'utilità di pianificazione

Dopo aver configurato il file con estensione XML di configurazione, è necessario configurare l'utilità di pianificazione per eseguire il file basicScaler.ps1 a intervalli regolari.

1. Avviare **utilità di pianificazione**.
2. Nel **utilità di pianificazione** finestra selezionare **Crea attività...**
3. Nella **l'attività di creazione** finestra di dialogo, seleziona la **generali** , immettere un **nome** (ad esempio, "Dynamic Host sessione Desktop remoto"), selezionare **eseguire anche se l'utente è connesso o non** e **eseguire con i privilegi più elevati**.
4. Andare alla **trigger** scheda e quindi selezionare **New...**
5. Nel **nuovo Trigger** finestra di dialogo, in **impostazioni avanzate**, selezionare **Ripeti l'attività ogni** e selezionare il periodo appropriato e la durata (ad esempio, **15 minuti** oppure **indefinitamente**).
6. Selezionare il **azioni** scheda e **New...**
7. Nel **nuova azione** finestra di dialogo immettere **powershell.exe** nel **programma/script** campo, quindi immettere **c:\\ridimensionamento\\ RDSScaler.ps1** nella **Aggiungi argomenti (facoltativo)** campo.
8. Andare alla **condizioni** e **impostazioni** schede e selezionare **OK** per accettare le impostazioni predefinite per ognuna.
9. Immettere la password per l'account amministrativo in cui si prevede di eseguire lo script di ridimensionamento.

## <a name="how-the-scaling-script-works"></a>Come funziona lo script scala

Questo script di ridimensionamento legge le impostazioni da un file config. XML, inclusi l'inizio e fine del periodo di utilizzo di picco del giorno.

Durante la fase di utilizzo di picco, lo script controlla il numero corrente di sessioni e la capacità di host sessione Desktop remoto in esecuzione corrente per ogni pool di host. Calcola se l'host di sessione in esecuzione le macchine virtuali hanno una capacità sufficiente per supportare le sessioni esistenti in base al parametro SessionThresholdPerCPU definito nel file config. Xml. In caso contrario, lo script Avvia sessione aggiuntivo host macchine virtuali nel pool di host.

Durante il periodo di periodi di minore attività, lo script determina quali host sessione macchine virtuali deve essere chiuso in base al parametro MinimumNumberOfRDSH nel file config. Xml. Lo script imposterà la sessione di ospitare le macchine virtuali per svuotare la modalità per impedire la connessione agli host nuove sessioni. Se si impostano i **LimitSecondsToForceLogOffUser** parametro nel file config. XML su un valore positivo diverso da zero, lo script notificherà eventuali già effettuato l'accesso agli utenti di risparmiare lavoro, il periodo di tempo di attesa e quindi forzare il disconnessione degli utenti. Dopo che tutte le sessioni utente sono state firmate in un host sessione macchina virtuale, lo script verrà arrestato il server.

Se si impostano i **LimitSecondsToForceLogOffUser** parametro nel file config. XML su zero, lo script consentirà l'impostazione di configurazione di sessione nell'host della proprietà del pool gestire l'approvazione delle sessioni utente. Se sono presenti tutte le sessioni in una macchina virtuale host sessione, verrà conservata l'host sessione macchina virtuale in esecuzione. Se non sono tutte le sessioni, lo script verrà arrestata la macchina virtuale host sessione.

Lo script è progettato per eseguire periodicamente nel server di macchine Virtuali di scaler l'utilità di pianificazione. Selezionare l'intervallo di tempo appropriato in base alla dimensione dell'ambiente di Servizi Desktop remoto e ricordare che avviare e arrestare le macchine virtuali può richiedere alcuni minuti. È consigliabile eseguire lo script scala ogni 15 minuti.

## <a name="log-files"></a>File di log

Lo script scala crea due file di log, **WVDTenantScale.log** e **WVDTenantUsage.log**. Il **WVDTenantScale.log** file registrerà gli eventi e gli errori (se presente) durante ogni esecuzione dello script di ridimensionamento.

Il **WVDTenantUsage.log** file registrerà il numero di core attivi e numero di macchine virtuali attivi ogni volta che si esegue lo script di ridimensionamento. È possibile usare queste informazioni per stimare l'utilizzo effettivo di macchine virtuali di Microsoft Azure e il costo. Il file viene formattato come valori delimitati da virgole, con ogni elemento che contiene le informazioni seguenti:

>ora, il pool di host, Core, le macchine virtuali

Il nome del file può essere modificato anche per con estensione csv, caricato in Microsoft Excel e analizzati.
