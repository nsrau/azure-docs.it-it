---
title: Distribuire la funzione della password di protezione di Azure AD disponibile in anteprima
description: Distribuire la funzione della password di protezione di Azure AD disponibile in anteprima per escludere le password non valide in locale
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5928896ab3c89972b7912f686be045afc988b1cd
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308876"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Anteprima: Distribuire la funzione della password di protezione di Azure AD

|     |
| --- |
| La password di protezione e l'elenco personalizzato di password escluse di Azure AD sono le funzioni in anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ora che è stato illustrato [come applicare la password di protezione di Azure AD per Windows Server Active Directory](concept-password-ban-bad-on-premises.md), il passaggio successivo consiste nel pianificare ed eseguire la distribuzione.

## <a name="deployment-strategy"></a>Strategia di distribuzione

Microsoft suggerisce che qualsiasi distribuzione venga avviata in modalità di controllo. La modalità di controllo è l'impostazione predefinita iniziale in cui è possibile continuare a impostare le password e quelle che vengono bloccate creano voci nel log eventi. Dopo avere completato la distribuzione del server o dei server proxy e degli agenti del controller di dominio in modalità di controllo, è necessario eseguire un monitoraggio regolare per determinare l'impatto dell'applicazione dei criteri password sugli utenti e sull'ambiente, se il criterio è stato applicato.

Durante la fase di controllo, molte organizzazioni rilevano che:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Gli utenti hanno l'abitudine di scegliere con frequenza password non sicure.
* È necessario informare gli utenti sulle future modifiche nell'applicazione della sicurezza, il possibile impatto sugli utenti e descrivere meglio come possono scegliere password più sicure.

Dopo che la funzione è stata in esecuzione in modalità di controllo per un tempo ragionevole, la configurazione di applicazione può essere invertita da **Controllo** ad **Applica** richiedendo in tal modo password più sicure. Il monitoraggio con lo stato attivo in questa fase è una scelta valida.

## <a name="known-limitation"></a>Limitazioni note

È nota una limitazione della versione di anteprima del proxy della password di protezione di Azure AD. Non è supportato l'utilizzo di account di amministratore per il tenant che richiedono l'autenticazione a più fattori. Un aggiornamento futuro del proxy della password di protezione di Azure AD supporterà la registrazione del proxy con gli account di amministratore che richiedono l'autenticazione a più fattori.

## <a name="single-forest-deployment"></a>Distribuzione della foresta singola

La funzione in anteprima della password di protezione di Active Directory viene distribuita con il servizio proxy su un massimo di due server e il servizio agente del controller di dominio può essere distribuito in modo incrementale su tutti i controller di domino nella foresta di Active Directory.

![Come si integrano i componenti della password di protezione di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Scaricare il software

Sono necessari due programmi di installazione per la password di protezione di Azure AD che possono essere scaricati dall'[area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installare e configurare il servizio proxy della password di protezione di Azure AD

1. Scegliere uno o più server per ospitare il servizio proxy della password di protezione di Azure AD.
   * Ogni servizio di questo tipo può fornire solo criteri password per una singola foresta e il computer host deve essere aggiunto a un dominio (i livelli radice e figlio sono supportati in ugual misura) in questa foresta. Per fare in modo che il servizio proxy della password di protezione di Azure AD svolga il proprio compito, deve esistere la connettività di rete tra almeno un controller di dominio in ogni dominio della foresta e il computer host proxy della password di protezione di Azure AD.
   * Supporta l'installazione e l'esecuzione del servizio proxy della password di protezione di Azure AD su un controller di dominio per scopi di test, ma il controller di dominio richiede in seguito la connettività Internet.

   > [!NOTE]
   > L'anteprima pubblica supporta un massimo di due (2) server proxy per ogni foresta.

2. Installare il software del servizio proxy dei criteri per le password usando il pacchetto MSI AzureADPasswordProtectionProxy.msi.
   * Non è necessario riavviare dopo l'installazione del software. L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Aprire una finestra di PowerShell come amministratore.
   * Il software proxy della password di protezione di Azure AD include AzureADPasswordProtection, un nuovo modulo di PowerShell. I passaggi seguenti si basano sull'esecuzione di diversi cmdlet da questo modulo di PowerShell, si presuppone che sia stata aperta una nuova finestra di PowerShell e che il nuovo modulo sia stato importato nel modo seguente:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > Il software di installazione modifica la variabile di ambiente PSModulePath del computer host. Affinché questa modifica venga applicata in modo da consentire l'importazione e l'utilizzo del modulo di PowerShell AzureADPasswordProtection, potrebbe essere necessario aprire una nuova finestra della console di PowerShell.

   * Verificare che il servizio sia in esecuzione usando il comando di PowerShell seguente: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * Il risultato deve essere **status** che restituisce il valore "Running".

4. Registrare il proxy.
   * Dopo avere completato il passaggio 3, il servizio proxy della password di protezione di Azure AD è in esecuzione sul computer, ma non dispone ancora delle credenziali necessarie per comunicare con Azure AD. La registrazione con Azure AD è necessaria per abilitare questa funzionalità tramite il cmdlet `Register-AzureADPasswordProtectionProxy` di PowerShell. Il cmdlet richiede le credenziali di amministratore globale per il tenant di Azure e i privilegi di amministratore del dominio Active Directory locale nel dominio radice della foresta. Dopo che la registrazione ha avuto esito positivo per uno specifico servizio proxy, ulteriori chiamate di `Register-AzureADPasswordProtectionProxy` continueranno ad avere esito positivo, ma non sono necessarie.
      * Il cmdlet può essere eseguito nel modo seguente:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         L'esempio funziona solo se l'utente attualmente connesso è anche amministratore del dominio Active Directory per il dominio radice. In alternativa è possibile specificare le credenziali di dominio tramite il parametro `-ForestCredential`.

   > [!TIP]
   > È possibile che si verifichi un ritardo di diversi secondi la prima volta che si esegue questo cmdlet per uno specifico tenant di Azure prima che il comando venga completato. A meno che non venga segnalato un errore, il ritardo non deve preoccupare.

   > [!NOTE]
   > Si presuppone che la registrazione del servizio proxy della password di protezione di Azure AD sia un passaggio da eseguire una sola volta nella durata del servizio. Il servizio proxy eseguirà automaticamente qualsiasi altra attività di manutenzione necessaria a partire da questo momento. Dopo che la registrazione ha avuto esito positivo per una specifica foresta, ulteriori chiamate di "Register-AzureADPasswordProtectionProxy" continueranno ad avere esito positivo, ma non sono necessarie.

5. Registrare la foresta.
   * La foresta di Active Directory locale deve essere inizializzata con le credenziali necessarie per comunicare con Azure tramite il cmdlet `Register-AzureADPasswordProtectionForest` di PowerShell. Il cmdlet richiede le credenziali di amministratore globale per il tenant di Azure e i privilegi di amministratore del dominio Active Directory locale nel dominio radice della foresta. Questo passaggio viene eseguito una volta per ogni foresta.
      * Il cmdlet può essere eseguito nel modo seguente:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         L'esempio funziona solo se l'utente attualmente connesso è anche amministratore del dominio Active Directory per il dominio radice. In alternativa è possibile specificare le credenziali di dominio tramite il parametro -ForestCredential.

         > [!NOTE]
         > Se nell'ambiente sono installati più server proxy, non è importante quale tra essi si aspecificato nella procedura sopra descritta.

         > [!TIP]
         > È possibile che si verifichi un ritardo di diversi secondi la prima volta che si esegue questo cmdlet per uno specifico tenant di Azure prima che il comando venga completato. A meno che non venga segnalato un errore, il ritardo non deve preoccupare.

   > [!NOTE]
   > Si presuppone che la registrazione della foresta di Active Directory sia un passaggio da eseguire una sola volta nella durata della foresta. Gli agenti del controller di dominio in esecuzione nella foresta eseguiranno automaticamente qualsiasi altra attività di manutenzione necessaria a partire da questo momento. Dopo che la registrazione ha avuto esito positivo per una specifica foresta, ulteriori chiamate di `Register-AzureADPasswordProtectionForest` continueranno ad avere esito positivo, ma non sono necessarie.

   > [!NOTE]
   > Affinché `Register-AzureADPasswordProtectionForest` abbia esito positivo, è necessario che nel dominio del server proxy sia disponibile almeno un controller di dominio Windows Server 2012 o versioni successive. Prima di questo passaggio, tuttavia, non è necessario che nel controller di dominio sia installato alcun software dell'agente del controller di dominio.

6. Facoltativo: configurare il servizio proxy della password di protezione di Azure AD per l'ascolto su una porta specifica.
   * Il meccanismo RPC su TCP viene usato dal software agente del controller di dominio della password di protezione di Azure AD nei controller di dominio per comunicare con il servizio proxy della password di protezione di Azure AD. Per impostazione predefinita, il servizio proxy dei criteri password di Azure AD è in ascolto su qualsiasi endpoint RPC dinamico disponibile. Se necessario a causa dei requisiti del firewall e della topologia di rete, il servizio potrebbe invece configurato per restare in attesa su una porta TCP specifica.
      * Per configurare il servizio per l'esecuzione su una porta statica, usare il cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

      * Per configurare il servizio per l'esecuzione su una porta dinamica, usare la stessa procedura ma impostare StaticPort di nuovo su zero, nel modo seguente:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

   > [!NOTE]
   > Dopo qualsiasi modifica della configurazione della porta è necessario riavviare manualmente il servizio proxy della password di protezione di Azure AD. Dopo questo tipo di modifiche della configurazione non è necessario riavviare il software del servizio agente del controller di dominio in esecuzione sui controller di dominio.

   * È possibile eseguire query sulla configurazione corrente del servizio usando il cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`, come illustrato nell'esempio seguente:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installare il servizio agente del controller di dominio della password di protezione di Azure AD.

* Installare il software del servizio agente del controller di dominio della password di protezione di Azure AD usando il pacchetto MSI `AzureADPasswordProtectionDCAgent.msi`:
   * L'installazione del software richiede un riavvio in fase di installazione e disinstallazione perché le DLL di filtro delle password vengono caricate o scaricate solo al riavvio per un requisito del sistema operativo.
   * È supportata l'installazione del servizio agente del controller di dominio su un computer che non è ancora un controller di dominio. In questo caso, il servizio verrà avviato ed eseguito, ma sarà inattivo fino all'innalzamento di livello del computer come controller di dominio.

   L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio: `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Il comando msiexec di esempio genererà un riavvio immediato che è possibile evitare specificando il flag `/norestart`.

Dopo avere eseguito l'installazione in un controller di dominio e avere riavviato, l'installazione del software dell'agente controller di dominio della password di protezione di Azure AD è stata completata. Non è necessario né possibile eseguire altre configurazioni.

## <a name="multiple-forest-deployments"></a>Distribuzioni di più foreste

Non sono previsti requisiti aggiuntivi per distribuire la password di protezione di Azure Active Directory in più foreste. Ogni foresta viene configurata in modo indipendente, come descritto nella sezione sulla distribuzione di singole foreste. Ogni proxy della password di protezione di Azure AD è in grado di supportare solo controller di dominio dalla foresta a cui è aggiunto. Il software della password di protezione di Azure AD in una specifica foresta non rileva la presenza del software della password di protezione di Azure AD distribuito in un'altra foresta, indipendentemente dalle configurazioni di trust di Active Directory.

## <a name="read-only-domain-controllers"></a>Controller di dominio di sola lettura

Le modifiche\impostazioni delle password non vengono mai elaborate e rese persistenti sui controller di dominio di sola lettura; al contrario vengono inoltrate ai controller di dominio scrivibili. Di conseguenza non è necessario installare il software del servizio agente del controller di dominio nei controller di dominio di sola lettura.

## <a name="high-availability"></a>Disponibilità elevata

Per garantire un'elevata disponibilità della funzione della password di protezione di Azure AD, l'aspetto strategico è la disponibilità dei server proxy, quando i controller di dominio in una foresta tentano di scaricare nuovi criteri o altri dati da Azure. L'anteprima pubblica supporta un massimo di due server proxy per ogni foresta. Ogni agente del controller di dominio usa un semplice algoritmo di tipo round-robin per decidere il server proxy da chiamare e ignora i server proxy che non rispondono.
I problemi associati in genere alla disponibilità elevata vengono mitigati con la progettazione del software del servizio agente del controller di dominio. L'agente del controller di dominio gestisce una cache locale dei criteri password scaricati più recenti. Anche se tutti i server proxy registrati diventano non disponibili per qualsiasi motivo, l'agente o gli agenti del controller di dominio continuano ad applicare i criteri delle password memorizzati nella cache. Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere nell'ordine dei giorni, non ore o intervalli inferiori.   Brevi interruzioni dei server proxy non causano quindi un impatto significativo sulla funzione della password di protezione di Azure AD o sui vantaggi a livello di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere installato i servizi necessari per la password di protezione di Azure AD sui server locali, completare la [configurazione successiva all'installazione e raccogliere le informazioni sui report](howto-password-ban-bad-on-premises-operations.md) per completare la distribuzione.

[Panoramica concettuale della password di protezione di Azure AD](concept-password-ban-bad-on-premises.md)
