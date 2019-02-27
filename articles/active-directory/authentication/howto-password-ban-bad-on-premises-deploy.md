---
title: Distribuire la funzionalità Protezione password di Azure AD disponibile in anteprima
description: Distribuire la funzionalità Protezione password di Azure AD disponibile in anteprima per escludere le password non valide in locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8d39b614c373c63cf1405c5db0f64581c481d1f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417204"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Anteprima: Distribuire la funzionalità Protezione password di Azure AD

|     |
| --- |
| Protezione password di Azure AD è una funzionalità di Azure Active Directory disponibile in anteprima pubblica. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Una volta compreso [come applicare la protezione password di Azure AD per Windows Server Active Directory](concept-password-ban-bad-on-premises.md), il passaggio successivo consiste nel pianificare ed eseguire la distribuzione.

## <a name="deployment-strategy"></a>Strategia di distribuzione

Microsoft suggerisce che qualsiasi distribuzione venga avviata in modalità di controllo. La modalità di controllo è l'impostazione predefinita iniziale in cui è possibile continuare a impostare le password e quelle che vengono bloccate creano voci nel log eventi. Dopo avere completato la distribuzione del server o dei server proxy e degli agenti del controller di dominio in modalità di controllo, è necessario eseguire un monitoraggio regolare per determinare l'impatto dell'applicazione dei criteri password sugli utenti e sull'ambiente, se il criterio è stato applicato.

Durante la fase di controllo, molte organizzazioni rilevano che:

* Hanno necessità di migliorare i processi operativi esistenti per usare password più sicure.
* Gli utenti hanno l'abitudine di scegliere con frequenza password non sicure.
* È necessario informare gli utenti sulle future modifiche nell'applicazione della sicurezza, il possibile impatto sugli utenti e descrivere meglio come possono scegliere password più sicure.

Dopo che la funzione è stata in esecuzione in modalità di controllo per un tempo ragionevole, la configurazione di applicazione può essere invertita da **Controllo** ad **Applica** richiedendo in tal modo password più sicure. Il monitoraggio con lo stato attivo in questa fase è una scelta valida.

## <a name="deployment-requirements"></a>Requisiti di distribuzione

* Tutti i controller di dominio in cui verrà installato il servizio agente del controller di dominio di Protezione password di Azure AD devono eseguire Windows Server 2012 o versioni successive.
* Tutti i computer in cui verrà installato il servizio proxy di Protezione password di Azure AD devono eseguire Windows Server 2012 R2 o versioni successive.
* Tutti i computer in cui sono installati i componenti di Protezione password di Azure AD, inclusi i controller di dominio, devono eseguire Universal C Runtime.
Questa condizione si ottiene preferibilmente con l'applicazione completa di patch alla macchina tramite Windows Update. In alternative, è possibile installare un pacchetto di aggiornamento appropriato specifico del sistema operativo. Vedere [Aggiornamento di Universal C Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)
* Deve esserci connettività di rete tra almeno un controller di dominio in ogni dominio e almeno un server che ospita il servizio proxy di Protezione password di Azure AD. Tale connettività deve consentire al controller di dominio di accedere alla porta del mapper di endpoint RPC (porta 135) e alla porta del server RPC nel servizio proxy. La porta del server RPC è per impostazione predefinita una porta RPC dinamica, ma può essere configurata (vedere sotto) per usare una porta statica.
* Tutti i computer che ospitano il servizio proxy di Protezione password di Azure AD devono disporre dell'accesso in rete agli endpoint seguenti:

    |Endpoint |Scopo|
    | --- | --- |
    |`https://login.microsoftonline.com`|Richieste di autenticazione|
    |`https://enterpriseregistration.windows.net`|Funzionalità di Protezione password di Azure AD|

* Tutti i computer che ospitano il servizio proxy di Protezione password di Azure AD devono essere configurati per consentire il traffico HTTP TLS 1.2 in uscita.
* Un account amministratore globale per registrare il servizio proxy di Protezione password di Azure AD e la foresta in Azure AD.
* Un account con privilegi di amministratore di dominio di Active Directory nel dominio radice della foresta per registrare la foresta di Windows Server Active Directory in Azure AD.
* Qualsiasi dominio di Active Directory che esegue il software del servizio agente del controller di dominio deve usare DFSR per la replica di sysvol.

## <a name="single-forest-deployment"></a>Distribuzione della foresta singola

Il diagramma seguente illustra l'interazione tra i componenti di base di Protezione password di Azure AD in un ambiente Active Directory locale.

![Come interagiscono i componenti di Protezione password di Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Prima della distribuzione è opportuno rivedere il funzionamento del software. Vedere [Panoramica dei concetti relativi a Protezione password di Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Scaricare il software

Per Protezione password di Azure AD sono necessari due programmi di installazione, che possono essere scaricati dall'[area download Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Installare e configurare il servizio proxy di Protezione password di Azure AD

1. Scegliere uno o più server per ospitare il servizio proxy di Protezione password di Azure AD.
   * Ogni servizio di questo tipo può fornire solo criteri delle password per una singola foresta e il computer host deve essere aggiunto a un dominio (i livelli radice e figlio sono supportati in ugual misura) in questa foresta. Per fare in modo che il servizio proxy di Protezione password di Azure AD svolga il proprio compito, deve esistere connettività di rete tra almeno un controller di dominio in ogni dominio della foresta e il computer proxy di Protezione password di Azure AD.
   * Sono supportate l'installazione e l'esecuzione del servizio proxy di Protezione password di Azure AD su un controller di dominio a scopo di test, ma il controller di dominio richiede in seguito la connettività Internet, cosa che può rappresentare un problema dal punto di vista della sicurezza. Microsoft consiglia di usare questa configurazione solo a scopo di test.
   * Sono consigliati almeno due server proxy per garantire la ridondanza. Vedere [Disponibilità elevata](howto-password-ban-bad-on-premises-deploy.md#high-availability).

2. Installare il servizio proxy di Protezione password di Azure AD usando il pacchetto MSI AzureADPasswordProtectionProxySetup.msi.
   * Non è necessario riavviare dopo l'installazione del software. L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio: `msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > Il servizio Windows Firewall deve essere in esecuzione prima di installare il pacchetto MSI AzureADPasswordProtectionProxySetup.msi, altrimenti si verificherà un errore di installazione. Se Windows Firewall è configurato per non essere eseguito, la soluzione consiste nell'abilitare e avviare temporaneamente il servizio Windows Firewall durante il processo di installazione. Il software Proxy non ha alcuna dipendenza specifica dal software Windows Firewall dopo l'installazione. Se si usa un firewall di terze parti, deve comunque essere configurato per soddisfare i requisiti di distribuzione (consentire l'accesso in ingresso alla porta 135 e alla porta del server RPC proxy dinamico o statico). [Vedere i requisiti di distribuzione](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. Aprire una finestra di PowerShell come amministratore.
   * Il software proxy di Protezione password di Azure AD include un nuovo modulo di PowerShell denominato AzureADPasswordProtection. I passaggi seguenti si basano sull'esecuzione di diversi cmdlet da questo modulo di PowerShell, si presuppone che sia stata aperta una nuova finestra di PowerShell e che il nuovo modulo sia stato importato nel modo seguente:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Verificare che il servizio sia in esecuzione usando il comando di PowerShell seguente: `Get-Service AzureADPasswordProtectionProxy | fl`.
     Il risultato deve essere **status** che restituisce il valore "Running".

4. Registrare il proxy.
   * Dopo avere completato il passaggio 3, il servizio proxy di Protezione password di Azure AD è in esecuzione nel computer, ma non dispone ancora delle credenziali necessarie per comunicare con Azure AD. La registrazione con Azure AD è necessaria per abilitare questa funzionalità tramite il cmdlet `Register-AzureADPasswordProtectionProxy` di PowerShell. Il cmdlet richiede le credenziali di amministratore globale per il tenant di Azure e i privilegi di amministratore del dominio Active Directory locale nel dominio radice della foresta. Dopo che la registrazione ha avuto esito positivo per uno specifico servizio proxy, ulteriori chiamate di `Register-AzureADPasswordProtectionProxy` continueranno ad avere esito positivo, ma non sono necessarie.

      Il cmdlet Register-AzureADPasswordProtectionProxy supporta tre diverse modalità di autenticazione, descritte di seguito.

      * Modalità di autenticazione interattiva:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Questa modalità non funziona nei sistemi operativi Server Core. Usare una delle modalità di autenticazione alternative descritte di seguito.

         > [!NOTE]
         > Questa modalità potrebbe non funzionare se è abilitata 	Configurazione sicurezza avanzata IE. La soluzione alternativa consiste nel disabilitare Sicurezza avanzata di Internet Explorer, registrare il proxy e quindi abilitare nuovamente Sicurezza avanzata di Internet Explorer.

      * Modalità di autenticazione basata sul codice del dispositivo:

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         È quindi possibile completare l'autenticazione seguendo le istruzioni visualizzate in un dispositivo diverso.

      * Modalità di autenticazione automatica (basata su password):

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Questa modalità non funziona se l'autenticazione richiede MFA per qualsiasi motivo. In tal caso usare una delle due modalità precedenti per eseguire un'autenticazione basata su MFA.

      Non è attualmente necessario specificare il parametro -ForestCredential, riservato per funzionalità future.

   > [!NOTE]
   > Si presuppone che la registrazione del servizio proxy di Protezione password di Azure AD sia un'operazione da eseguire una sola volta in tutta la durata del servizio. Il servizio proxy eseguirà automaticamente qualsiasi altra attività di manutenzione necessaria a partire da questo momento. Dopo che la registrazione ha avuto esito positivo per un determinato proxy, ulteriori chiamate di "Register-AzureADPasswordProtectionProxy" continuano ad avere esito positivo, ma non sono necessarie.

   > [!TIP]
   > È possibile che si verifichi un ritardo di diversi secondi la prima volta che si esegue questo cmdlet per uno specifico tenant di Azure prima che il comando venga completato. A meno che non venga segnalato un errore, il ritardo non deve preoccupare.

5. Registrare la foresta.
   * La foresta di Active Directory locale deve essere inizializzata con le credenziali necessarie per comunicare con Azure tramite il cmdlet `Register-AzureADPasswordProtectionForest` di PowerShell. Il cmdlet richiede le credenziali di amministratore globale per il tenant di Azure e i privilegi di amministratore del dominio Active Directory locale nel dominio radice della foresta. Questo passaggio viene eseguito una volta per ogni foresta.

      Il cmdlet Register-AzureADPasswordProtectionForest supporta tre diverse modalità di autenticazione, descritte di seguito.

      * Modalità di autenticazione interattiva:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > Questa modalità non funziona nei sistemi operativi Server Core. Usare una delle modalità di autenticazione alternative descritte di seguito.

         > [!NOTE]
         > Questa modalità potrebbe non funzionare se è abilitata 	Configurazione sicurezza avanzata IE. La soluzione alternativa consiste nel disabilitare Sicurezza avanzata di Internet Explorer, registrare il proxy e quindi abilitare nuovamente Sicurezza avanzata di Internet Explorer.  

      * Modalità di autenticazione basata sul codice del dispositivo:

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         È quindi possibile completare l'autenticazione seguendo le istruzioni visualizzate in un dispositivo diverso.

      * Modalità di autenticazione automatica (basata su password):
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > Questa modalità non funziona se l'autenticazione richiede MFA. In tal caso usare una delle due modalità precedenti per eseguire un'autenticazione basata su MFA.

      Gli esempi riportati sopra funzionano solo se l'utente attualmente connesso è anche amministratore del dominio Active Directory per il dominio radice. In caso contrario, è possibile specificare credenziali di dominio alternative tramite il parametro -ForestCredential.

   > [!NOTE]
   > Se nell'ambiente sono installati più server proxy, non è importante quale viene usato per registrare la foresta.

   > [!TIP]
   > È possibile che si verifichi un ritardo di diversi secondi la prima volta che si esegue questo cmdlet per uno specifico tenant di Azure prima che il comando venga completato. A meno che non venga segnalato un errore, il ritardo non deve preoccupare.

   > [!NOTE]
   > Si presuppone che la registrazione della foresta di Active Directory sia un passaggio da eseguire una sola volta nella durata della foresta. Gli agenti del controller di dominio in esecuzione nella foresta eseguiranno automaticamente qualsiasi altra attività di manutenzione necessaria a partire da questo momento. Dopo che la registrazione ha avuto esito positivo per una specifica foresta, ulteriori chiamate di `Register-AzureADPasswordProtectionForest` continueranno ad avere esito positivo, ma non sono necessarie.

   > [!NOTE]
   > Affinché `Register-AzureADPasswordProtectionForest` abbia esito positivo, è necessario che nel dominio del server proxy sia disponibile almeno un controller di dominio Windows Server 2012 o versioni successive. Prima di questo passaggio, tuttavia, non è necessario che nel controller di dominio sia installato alcun software dell'agente del controller di dominio.

6. Configurare il servizio proxy di Protezione password di Azure AD per comunicare tramite un proxy HTTP

   Se l'ambiente richiede l'uso di uno specifico proxy HTTP per comunicare con Azure, è possibile procedere nel modo seguente.

   Creare un file denominato `proxyservice.exe.config` nella cartella `%ProgramFiles%\Azure AD Password Protection Proxy\Service` con il contenuto seguente:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Se il proxy HTTP richiede l'autenticazione, aggiungere il tag useDefaultCredentials come segue:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   In entrambi i casi sostituire `http://yourhttpproxy.com:8080` con l'indirizzo e la porta del server proxy HTTP specifico.

   Se il proxy HTTP è configurato con un criterio di autorizzazione, è necessario concedere l'accesso all'account computer Active Directory del computer che ospita il servizio proxy di Protezione password di Azure AD.

   Dopo aver creato o aggiornato il file `proxyservice.exe.config` è necessario arrestare e riavviare il servizio proxy di Protezione password di Azure AD.

   Il servizio proxy di Protezione password di Azure AD non supporta l'uso di credenziali specifiche per la connessione a un proxy HTTP.

7. Facoltativo: Configurare il servizio proxy di Protezione password di Azure AD per l'ascolto su una porta specifica.
   * Il meccanismo RPC su TCP viene usato dal software agente del controller di dominio di Protezione password di Azure AD nei controller di dominio per comunicare con il servizio proxy di Protezione password di Azure AD. Per impostazione predefinita, il servizio proxy di Protezione password di Azure AD è in ascolto su qualsiasi endpoint RPC dinamico disponibile. Se necessario a causa dei requisiti del firewall e della topologia di rete, il servizio potrebbe invece configurato per restare in attesa su una porta TCP specifica.
      * Per configurare il servizio per l'esecuzione su una porta statica, usare il cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

      * Per configurare il servizio per l'esecuzione su una porta dinamica, usare la stessa procedura ma impostare StaticPort di nuovo su zero, nel modo seguente:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > È necessario arrestare e riavviare il servizio per applicare le modifiche.

   > [!NOTE]
   > Dopo qualsiasi modifica della configurazione della porta è necessario riavviare manualmente il servizio proxy di Protezione password di Azure AD. Dopo questo tipo di modifiche della configurazione non è necessario riavviare il software del servizio agente del controller di dominio in esecuzione sui controller di dominio.

   * È possibile eseguire query sulla configurazione corrente del servizio usando il cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`, come illustrato nell'esempio seguente:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Installare il servizio agente del controller di dominio di Protezione password di Azure AD

   Installare il software del servizio agente del controller di dominio di Protezione password di Azure AD usando il pacchetto MSI `AzureADPasswordProtectionDCAgent.msi`

   L'installazione del software richiede un riavvio in fase di installazione e disinstallazione perché le DLL di filtro delle password vengono caricate o scaricate solo al riavvio per un requisito del sistema operativo.

   È supportata l'installazione del servizio agente del controller di dominio su un computer che non è ancora un controller di dominio. In questo caso, il servizio verrà avviato ed eseguito, ma sarà inattivo fino all'innalzamento di livello del computer come controller di dominio.

   L'installazione del software può essere automatizzata usando le procedure MSI standard, ad esempio:

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > Il comando msiexec di esempio riportato sopra causerà un riavvio immediato, che può essere evitato specificando il flag `/norestart`.

Dopo avere eseguito l'installazione in un controller di dominio e aver riavviato il sistema, l'installazione del software dell'agente controller di dominio di Protezione password di Azure AD è completata. Non è necessario né possibile eseguire altre configurazioni.

## <a name="multiple-forest-deployments"></a>Distribuzioni di più foreste

Non sono previsti requisiti aggiuntivi per distribuire Protezione password di Azure AD in più foreste. Ogni foresta viene configurata in modo indipendente, come descritto nella sezione sulla distribuzione di singole foreste. Ogni proxy di Protezione password di Azure AD supporta solo controller di dominio della foresta a cui è aggiunto. Il software di Protezione password di Azure AD in una specifica foresta non rileva la presenza del software di Protezione password di Azure AD distribuito in un'altra foresta, indipendentemente dalle configurazioni di trust di Active Directory.

## <a name="read-only-domain-controllers"></a>Controller di dominio di sola lettura

Le modifiche\impostazioni delle password non vengono mai elaborate e rese persistenti sui controller di dominio di sola lettura; al contrario vengono inoltrate ai controller di dominio scrivibili. Di conseguenza non è necessario installare il software del servizio agente del controller di dominio nei controller di dominio di sola lettura.

## <a name="high-availability"></a>Disponibilità elevata

Per garantire un'elevata disponibilità di Protezione password di Azure AD, l'aspetto strategico è la disponibilità dei server proxy, quando i controller di dominio in una foresta tentano di scaricare nuovi criteri o altri dati da Azure. Ogni agente del controller di dominio usa un semplice algoritmo di tipo round-robin per decidere il server proxy da chiamare e ignora i server proxy che non rispondono. Per la maggior parte delle distribuzioni di Active Directory completamente connesse con replica integra (sia dello stato della directory che di SYSVOL), due (2) server proxy dovrebbero essere sufficienti ad assicurare la disponibilità, garantendo pertanto download tempestivi dei nuovi criteri e di altri dati. Se necessario, è possibile distribuire altri server proxy.

I problemi associati in genere alla disponibilità elevata vengono mitigati con la progettazione del software del servizio agente del controller di dominio. L'agente del controller di dominio gestisce una cache locale dei criteri password scaricati più recenti. Anche se tutti i server proxy registrati diventano non disponibili per qualsiasi motivo, l'agente o gli agenti del controller di dominio continuano ad applicare i criteri delle password memorizzati nella cache. Una frequenza di aggiornamento ragionevole per i criteri password in una distribuzione di grandi dimensioni è in genere nell'ordine dei giorni, non ore o intervalli inferiori. Brevi interruzioni dei server proxy non causano quindi un impatto significativo sulla funzione della password di protezione di Azure AD o sui vantaggi a livello di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere installato i servizi necessari per Protezione password di Azure AD sui server locali, completare la [configurazione successiva all'installazione e raccogliere le informazioni sui report](howto-password-ban-bad-on-premises-operations.md) per completare la distribuzione.

[Panoramica dei concetti relativi a Protezione password di Azure AD](concept-password-ban-bad-on-premises.md)
