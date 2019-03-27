---
title: Ridimensionare automaticamente gli host della sessione di anteprima di Desktop virtuale Windows - Azure
description: Viene descritto come configurare lo script di scalabilità automatica per gli host della sessione di Windows Virtual Desktop Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 379e73c33aa4570c3e56f902b011d75944c94a8d
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497898"
---
# <a name="automatically-scale-session-hosts"></a>Ridimensionare automaticamente gli host della sessione

Per molte distribuzioni di anteprima di Desktop virtuale Windows in Azure, i costi di macchine virtuali rappresentano una parte significativa del costo totale distribuzione Desktop virtuale Windows. Per ridurre i costi, è consigliabile arrestare e deallocare sessione ospitare macchine virtuali (VM) durante le ore non di punta, quindi riavviarle durante le ore di punta.

Questo articolo usa un semplice script di ridimensionamento per ridimensionare automaticamente macchine virtuali host sessione Desktop virtuale Windows nell'ambiente in uso. Per altre informazioni su come funziona lo script di ridimensionamento, vedere la [come funziona lo script scala](#how-the-scaling-script-works) sezione.

## <a name="prerequisites"></a>Prerequisiti

L'ambiente in cui si esegue lo script deve avere le operazioni seguenti:

- Un tenant di Desktop virtuale Windows e account o un'entità servizio con le autorizzazioni per eseguire query sui tenant (ad esempio collaboratore di servizi desktop remoto).
- Macchine virtuali del pool host sessione configurato e registrato con il servizio di Windows Desktop virtuale.
- Un scaler aggiuntive della macchina virtuale che esegue l'attività pianificata tramite utilità di pianificazione e che abbia accesso alla rete agli host della sessione.
- Il modulo PowerShell di Resource Manager di Microsoft Azure installato nella macchina virtuale che esegue l'attività pianificata.
- Il modulo Windows PowerShell di Desktop virtuale installato nella macchina virtuale che esegue l'attività pianificata.

## <a name="recommendations-and-limitations"></a>Indicazioni e limitazioni

Quando si esegue lo script scala, tenere presente quanto segue:

- Questo script di ridimensionamento può gestire solo un pool di host per ogni istanza dell'attività pianificata che esegue lo script di ridimensionamento.
- Le attività pianificate che eseguono gli script di ridimensionamento devono trovarsi in una macchina virtuale che è sempre attiva.
- Creare una cartella separata per ogni istanza di ridimensionamento script e la relativa configurazione.
- Questo script non supporta gli account di multi-factor Authentication. È consigliabile che usare le entità servizio per accedere al servizio di Desktop virtuale Windows e Azure.
- Garanzia di contratto di servizio di Azure si applica solo alle macchine virtuali nel set di disponibilità. La versione corrente del documento viene descritto un ambiente con una singola macchina virtuale esegue il ridimensionamento, che potrebbe non soddisfare i requisiti di disponibilità.

## <a name="deploy-the-scaling-script"></a>Distribuire lo script di ridimensionamento

Le procedure seguenti illustreranno come distribuire lo script di ridimensionamento.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Preparare l'ambiente per lo script di ridimensionamento

Prima di tutto, preparare l'ambiente per lo script scala:

1. Accedere alla macchina virtuale (**scalabilità della macchina virtuale**) che verrà eseguito l'attività pianificata con un account amministrativo di dominio.
2. Creare una cartella nella macchina virtuale per contenere lo script di ridimensionamento e la relativa configurazione della scalabilità (ad esempio, **c:\\HostPool1 ridimensionamento**).
3. Scaricare il **basicScaler.ps1**, **config. XML**, e **funzioni PSStoredCredentials.ps1** file e il **PowershellModules** cartella dal [ridimensionamento nello script repository](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) e copiarli nella cartella creata nel passaggio 2.

### <a name="create-securely-stored-credentials"></a>Creare le credenziali archiviate in modo sicuro

Successivamente, è necessario creare le credenziali archiviate in modo sicuro:

1. Aprire PowerShell ISE come amministratore.
2. Aprire il riquadro di modifica e caricare il **funzione PSStoredCredentials.ps1** file.
3. Eseguire il cmdlet seguente:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Ad esempio, **Set-Variable - Name KeyPath-ambito globale-valore "c:\\HostPool1 scalabilità"**
4. Eseguire la **New-StoredCredential - KeyPath \$KeyPath** cmdlet. Quando richiesto, immettere le credenziali di Desktop virtuale Windows con le autorizzazioni per eseguire una query il pool di host (il pool di host è specificato nella **config. XML**).
    - Se si usa diverse entità servizio o account standard, eseguire la **New-StoredCredential - KeyPath \$KeyPath** cmdlet una volta per ogni account creare locale le credenziali archiviate.
5. Eseguire **Get-StoredCredentials-elenco** per confermare le credenziali sono state create correttamente.

### <a name="configure-the-configxml-file"></a>Configurare il file config. Xml

Immettere i valori appropriati nei campi seguenti per aggiornare le impostazioni di scalabilità script nel file config. XML:

| Campo                     | DESCRIZIONE                    |
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
| SessionThresholdPerCPU        | Numero massimo di sessioni per ogni valore soglia della CPU utilizzato per determinare quando un nuovo server host sessione Desktop remoto deve essere avviato durante le ore di picco.  |
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

Durante la fase di utilizzo di picco, lo script controlla il numero corrente di sessioni e la capacità di host sessione Desktop remoto in esecuzione corrente per ogni raccolta. Calcola i server host sessione Desktop remoto in esecuzione la presenza di una capacità sufficiente per supportare le sessioni esistenti in base al parametro SessionThresholdPerCPU definito nel file config. Xml. In caso contrario, lo script avvia ulteriori server host sessione Desktop remoto nella raccolta.

Durante il periodo di periodi di minore attività, lo script determina quali server host sessione Desktop remoto deve essere chiuso basato sul parametro MinimumNumberOfRDSH nel file config. Xml. Lo script verrà impostato il server host sessione Desktop remoto per svuotare la modalità per impedire la connessione agli host nuove sessioni. Se si impostano i **LimitSecondsToForceLogOffUser** parametro nel file config. XML su un valore positivo diverso da zero, lo script notificherà eventuali già effettuato l'accesso agli utenti di risparmiare lavoro, il periodo di tempo di attesa e quindi forzare il disconnessione degli utenti. Dopo che tutte le sessioni utente sono state firmate in un server host sessione Desktop remoto, lo script verrà arrestato il server.

Se si impostano i **LimitSecondsToForceLogOffUser** parametro nel file config. XML su zero, lo script consentirà l'impostazione di configurazione di sessione nelle proprietà della raccolta per gestire l'approvazione delle sessioni utente. Se sono presenti tutte le sessioni in un server host sessione Desktop remoto, rimarrà il server host sessione Desktop remoto in esecuzione. Se non sono presenti tutte le sessioni, lo script si arresta il server host sessione Desktop remoto.

Lo script è progettato per eseguire periodicamente nel server di macchine Virtuali di scaler l'utilità di pianificazione. Selezionare l'intervallo di tempo appropriato in base alla dimensione dell'ambiente di Servizi Desktop remoto e ricordare che avviare e arrestare le macchine virtuali può richiedere alcuni minuti. È consigliabile eseguire lo script scala ogni 15 minuti.

## <a name="log-files"></a>File di log

Lo script scala crea due file di log, **WVDTenantScale.log** e **WVDTenantUsage.log**. Il **WVDTenantScale.log** file registrerà gli eventi e gli errori (se presente) durante ogni esecuzione dello script di ridimensionamento.

Il **WVDTenantUsage.log** file registrerà il numero di core attivi e numero di macchine virtuali attivi ogni volta che si esegue lo script di ridimensionamento. È possibile usare queste informazioni per stimare l'utilizzo effettivo di macchine virtuali di Microsoft Azure e il costo. Il file viene formattato come valori delimitati da virgole, con ogni elemento che contiene le informazioni seguenti:

>ora, insieme, Core, le macchine virtuali

Il nome del file può essere modificato anche per con estensione csv, caricato in Microsoft Excel e analizzati.