---
title: Come importare ed esportare le impostazioni di configurazione Azure AD Connect
description: Questo articolo descrive le domande frequenti per il provisioning cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da80af9fe598186fa25d59601c9fa4faccb4286a
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447052"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>Importare ed esportare le impostazioni di configurazione Azure AD Connect (anteprima pubblica)

Le distribuzioni di Azure Active Directory (Azure AD) Connect variano da un'installazione in modalità Express a foresta singola a distribuzioni complesse che si sincronizzano tra più foreste usando regole di sincronizzazione personalizzate. A causa dell'elevato numero di opzioni e meccanismi di configurazione, è essenziale comprendere quali impostazioni sono attive e poter distribuire rapidamente un server con una configurazione identica. Questa funzionalità introduce la possibilità di catalogare la configurazione di un determinato server di sincronizzazione e di importare le impostazioni in una nuova distribuzione. È possibile confrontare snapshot di impostazioni di sincronizzazione diversi per visualizzare facilmente le differenze tra due server o lo stesso server nel tempo.

Ogni volta che la configurazione viene modificata dalla procedura guidata Azure AD Connect, un nuovo file di impostazioni JSON timestamp viene automaticamente esportato in **%ProgramData%\AADConnect**. Il formato del nome del file di impostazioni è **Applied-SynchronizationPolicy-*. JSON**, dove l'ultima parte del nome file è un timestamp.

> [!IMPORTANT]
> Vengono esportate automaticamente solo le modifiche apportate da Azure AD Connect. Tutte le modifiche apportate tramite PowerShell, il Synchronization Service Manager o l'editor delle regole di sincronizzazione devono essere esportate su richiesta, in base alle esigenze, per mantenere una copia aggiornata. L'esportazione su richiesta può essere usata anche per inserire una copia delle impostazioni in un luogo sicuro a scopo di ripristino di emergenza.

## <a name="export-azure-ad-connect-settings"></a>Esporta impostazioni Azure AD Connect 

Per visualizzare un riepilogo delle impostazioni di configurazione, aprire lo strumento Azure AD Connect e selezionare l'attività aggiuntiva denominata **Visualizza o Esporta configurazione corrente**. Viene visualizzato un breve riepilogo delle impostazioni insieme alla possibilità di esportare la configurazione completa del server.

Per impostazione predefinita, le impostazioni vengono esportate in **%ProgramData%\AADConnect**. È anche possibile scegliere di salvare le impostazioni in un percorso protetto per garantire la disponibilità in caso di emergenza. Le impostazioni vengono esportate usando il formato di file JSON e non devono essere create manualmente o modificate per garantire la coerenza logica. L'importazione di un file creato manualmente o modificato non è supportata e può causare risultati imprevisti.

## <a name="import-azure-ad-connect-settings"></a>Importa impostazioni Azure AD Connect

Per importare le impostazioni esportate in precedenza:
 
1. Installare **Azure ad Connect** in un nuovo server.
1. Selezionare l'opzione **Personalizza** dopo la pagina **iniziale** .
1. Selezionare **Importa impostazioni di sincronizzazione**. Cercare il file di impostazioni JSON esportato in precedenza.
1. Selezionare **Installa**.

   ![Screenshot che mostra la schermata Installa componenti necessari](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Eseguire l'override delle impostazioni in questa pagina come l'utilizzo di SQL Server anziché del database locale o l'utilizzo di un account del servizio esistente anziché di un valore VSA predefinito. Queste impostazioni non vengono importate dal file delle impostazioni di configurazione. Sono disponibili a scopo informativo e di confronto.

### <a name="import-installation-experience"></a>Importa l'esperienza di installazione 

L'esperienza di installazione dell'importazione viene intenzionalmente mantenuta semplice con input minimi dall'utente per fornire facilmente la riproducibilità di un server esistente.

Ecco le uniche modifiche che possono essere apportate durante l'esperienza di installazione. Tutte le altre modifiche possono essere apportate dopo l'installazione dalla procedura guidata Azure AD Connect:
- **Azure Active Directory credenziali**: il nome dell'account per l'amministratore globale di Azure usato per configurare il server originale è suggerito per impostazione predefinita. È *necessario*   modificarlo se si desidera sincronizzare le informazioni in una nuova directory.
- **Accesso utente**: le opzioni di accesso configurate per il server originale sono selezionate per impostazione predefinita e richiedono automaticamente le credenziali o altre informazioni necessarie durante la configurazione. In rari casi, potrebbe essere necessario configurare un server con diverse opzioni per evitare di modificare il comportamento del server attivo. In caso contrario, selezionare **Avanti** per usare le stesse impostazioni.
- **Credenziali della directory locale**: per ogni directory locale inclusa nelle impostazioni di sincronizzazione, è necessario fornire le credenziali per creare un account di sincronizzazione o fornire un account di sincronizzazione personalizzato creato in precedenza. Questa procedura è identica all'esperienza di installazione pulita, con l'eccezione che non è possibile aggiungere o rimuovere directory.
- **Opzioni di configurazione**: come con un'installazione pulita, è possibile scegliere di configurare le impostazioni iniziali per stabilire se avviare la sincronizzazione automatica o abilitare la modalità di gestione temporanea. La differenza principale è che la modalità di gestione temporanea è intenzionalmente abilitata per impostazione predefinita per consentire il confronto dei risultati della configurazione e della sincronizzazione prima di esportare attivamente i risultati in Azure.

![Screenshot che mostra la schermata Connetti le directory](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Solo un server di sincronizzazione può trovarsi nel ruolo primario ed esportare attivamente le modifiche di configurazione in Azure. Tutti gli altri server devono essere posizionati in modalità di staging.

## <a name="migrate-settings-from-an-existing-server"></a>Migrare le impostazioni da un server esistente 

Se un server esistente non supporta la gestione delle impostazioni, è possibile scegliere di aggiornare il server sul posto o eseguire la migrazione delle impostazioni per l'utilizzo in un nuovo server di staging.

Per la migrazione è necessario eseguire uno script di PowerShell che estrae le impostazioni esistenti per l'utilizzo in una nuova installazione.Utilizzare questo metodo per catalogare le impostazioni del server esistente e quindi applicarle a un server di staging appena installato.Il confronto tra le impostazioni per il server originale e un server appena creato consente di visualizzare rapidamente le modifiche tra i server.Come sempre, seguire il processo di certificazione dell'organizzazione per assicurarsi che non sia necessaria alcuna configurazione aggiuntiva.

### <a name="migration-process"></a>Processo di migrazione 
Per eseguire la migrazione delle impostazioni:

1. Avviare **AzureADConnect.msi** nel nuovo server di gestione temporanea e arrestare la pagina **iniziale** di Azure ad Connect.

1. Copiare **MigrateSettings.ps1** dalla directory Microsoft Azure ad Connect\Tools in un percorso nel server esistente. Un esempio è C:\setup, in cui il programma di installazione è una directory creata nel server esistente.

   ![Screenshot che mostra le directory Azure AD Connect.](media/how-to-connect-import-export-config/migrate1.png)

1. Eseguire lo script come illustrato qui e salvare l'intera directory di configurazione del server di livello inferiore. Copiare questa directory nel nuovo server di gestione temporanea. È necessario copiare l'intera cartella **esportata-ServerConfiguration-*** nel nuovo server.

   ![Screenshot che mostra lo script in Windows PowerShell. ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Screenshot che mostra la copia della cartella Exported-ServerConfiguration-*.](media/how-to-connect-import-export-config/migrate3.png)

1. Avviare **Azure ad Connect** facendo doppio clic sull'icona sul desktop. Accettare le condizioni di licenza software Microsoft e, nella pagina successiva, selezionare **Personalizza**.
1. Selezionare la casella di controllo **Importa impostazioni di sincronizzazione** . Selezionare **Sfoglia** per visualizzare la cartella copiato-over esportated-ServerConfiguration-*. Selezionare il MigratedPolicy.jssu per importare le impostazioni migrate.

   ![Screenshot che mostra l'opzione Importa impostazioni di sincronizzazione.](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>Verifica post-installazione 

Il confronto tra il file di impostazioni originariamente importato e il file di impostazioni esportato del server appena distribuito è un passaggio essenziale per comprendere eventuali differenze tra la distribuzione desiderata e quella risultante. L'uso dell'applicazione di confronto di testo affiancata preferita produce una visualizzazione immediata che evidenzia rapidamente eventuali modifiche desiderate o accidentali.

Sebbene molti passaggi di configurazione precedentemente manuali vengano eliminati, è comunque necessario seguire il processo di certificazione dell'organizzazione per assicurarsi che non siano necessarie configurazioni aggiuntive. Questa configurazione può verificarsi se si usano impostazioni avanzate, che non sono attualmente acquisite nella versione di anteprima pubblica di gestione impostazioni.

Di seguito sono riportate alcune limitazioni note:
- **Regole di sincronizzazione**: la precedenza per una regola personalizzata deve essere nell'intervallo riservato compreso tra 0 e 99 per evitare conflitti con le regole standard di Microsoft. L'inserimento di una regola personalizzata al di fuori dell'intervallo riservato può comportare la spostamento della regola personalizzata in base alle regole standard aggiunte alla configurazione. Se la configurazione contiene regole standard modificate, si verificherà un problema simile. La modifica di una regola standard è sconsigliata e il posizionamento delle regole potrebbe non essere corretto.
- **Writeback dei dispositivi**: queste impostazioni sono catalogate. Non sono attualmente applicati durante la configurazione. Se il writeback dei dispositivi è stato abilitato per il server originale, è necessario configurare manualmente la funzionalità nel server appena distribuito.
- **Tipi di oggetti sincronizzati**: Sebbene sia possibile vincolare l'elenco di tipi di oggetti sincronizzati, ad esempio utenti, contatti e gruppi, usando il Synchronization Service Manager, questa funzionalità non è attualmente supportata tramite le impostazioni di sincronizzazione. Al termine dell'installazione, è necessario riapplicare manualmente la configurazione avanzata.
- **Profili di esecuzione personalizzati**: Sebbene sia possibile modificare il set predefinito di profili di esecuzione usando il Synchronization Service Manager, questa funzionalità non è attualmente supportata tramite le impostazioni di sincronizzazione. Al termine dell'installazione, è necessario riapplicare manualmente la configurazione avanzata.
- **Configurazione della gerarchia di provisioning**: questa funzionalità avanzata del Synchronization Service Manager non è supportata tramite le impostazioni di sincronizzazione. È necessario riconfigurarlo manualmente dopo aver completato la distribuzione iniziale.
- **Active Directory Federation Services (ad FS) e autenticazione PingFederate**: i metodi di accesso associati a queste funzionalità di autenticazione vengono preselezionati automaticamente. È necessario fornire in modo interattivo tutti gli altri parametri di configurazione necessari.
- **Una regola di sincronizzazione personalizzata disabilitata verrà importata come abilitata**: una regola di sincronizzazione personalizzata disabilitata viene importata come abilitata. Assicurarsi di disabilitarlo anche nel nuovo server.

 ## <a name="next-steps"></a>Passaggi successivi

- [Hardware e prerequisiti](how-to-connect-install-prerequisites.md) 
- [Impostazioni rapide](how-to-connect-install-express.md)
- [Impostazioni personalizzate](how-to-connect-install-custom.md)
- [Installare gli agenti di Azure AD Connect Health](how-to-connect-health-agent-install.md) 
