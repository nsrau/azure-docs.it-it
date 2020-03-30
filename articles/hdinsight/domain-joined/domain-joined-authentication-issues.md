---
title: Problemi di autenticazione in Azure HDInsightAuthentication issues in Azure HDInsight
description: Problemi di autenticazione in Azure HDInsightAuthentication issues in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 26eec9cdd327ceb51e72deb1d6f40d585ce368fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896130"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemi di autenticazione in Azure HDInsightAuthentication issues in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

Nei cluster protetti supportati da Azure Data Lake (Gen1 o Gen2), quando gli utenti di dominio accedono ai servizi cluster tramite gateway HDI (ad esempio l'accesso al portale Apache Ambari), Gateway HDI tenterà di ottenere prima un token OAuth da Azure Active Directory (Azure AD) , quindi ottenere un ticket Kerberos da Azure AD DS. L'autenticazione può avere esito negativo in una di queste fasi. Questo articolo ha lo scopo di eseguire il debug di alcuni di questi problemi.

Quando l'autenticazione non riesce, verranno richieste le credenziali. Se si annulla questa finestra di dialogo, verrà stampato il messaggio di errore. Ecco alcuni dei messaggi di errore comuni:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant o unauthorized_client, 50126

### <a name="issue"></a>Problema

L'accesso non riesce per gli utenti federati con codice di errore 50126 (l'accesso ha esito positivo per gli utenti cloud). Il messaggio di errore è simile al:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

Il codice di errore 50126 di Azure AD indica che i `AllowCloudPasswordValidation` criteri non sono stati impostati dal tenant.

### <a name="resolution"></a>Risoluzione

L'amministratore aziendale del tenant di Azure AD deve consentire ad Azure AD di usare gli zocmi delle password per gli utenti supportati da ADFS.  Applicare `AllowCloudPasswordValidationPolicy` l'oggetto come illustrato nell'articolo [Use Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant o unauthorized_client, 50034

### <a name="issue"></a>Problema

L'accesso non riesce con codice di errore 50034. Il messaggio di errore è simile al:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

Il nome utente non è corretto (non esiste). L'utente non usa lo stesso nome utente usato nel portale di Azure.The user is not using the same username that is used in Azure portal.

### <a name="resolution"></a>Risoluzione

Usare lo stesso nome utente che funziona nel portale.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant o unauthorized_client, 50053

### <a name="issue"></a>Problema

Account utente è bloccato, codice di errore 50053. Il messaggio di errore è simile al:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Troppi tentativi di accesso con una password errata.

### <a name="resolution"></a>Risoluzione

Attendere circa 30 minuti, arrestare tutte le applicazioni che potrebbero tentare di eseguire l'autenticazione.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant o unauthorized_client, 50053

### <a name="issue"></a>Problema

Password scaduta, codice di errore 50053. Il messaggio di errore è simile al:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

La password è scaduta.

### <a name="resolution"></a>Risoluzione

Modificare la password nel portale di Azure (nel sistema locale) e quindi attendere 30 minuti affinché la sincronizzazione venga aggiornata.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Messaggio di `interaction_required`errore di ricezione .

### <a name="cause"></a>Causa

L'autenticazione a più fattori o i criteri di accesso condizionale vengono applicati all'utente. Poiché l'autenticazione interattiva non è ancora supportata, l'utente o il cluster deve essere esentato dall'accesso condizionale/con autenticazione a più fattori. Se si sceglie di escludere il cluster (criterio di esenzione basato su indirizzo IP), assicurarsi che Active Directory `ServiceEndpoints` sia abilitato per tale rete virtuale.

### <a name="resolution"></a>Risoluzione

Usare i criteri di accesso condizionale ed esentare i cluster HDInisght dall'autenticazione a più fattori, come illustrato in [Configurare un cluster HDInsight con Enterprise Security Package tramite Servizi](./apache-domain-joined-configure-using-azure-adds.md)di dominio Azure Active Directory.

---

## <a name="sign-in-denied"></a>Accesso negato

### <a name="issue"></a>Problema

L'accesso è negato.

### <a name="cause"></a>Causa

Per arrivare a questa fase, l'autenticazione OAuth non è un problema, ma l'autenticazione Kerberos lo è. Se questo cluster è supportato da ADLS, l'accesso OAuth è riuscito prima che venga tentata l'autenticazione Kerberos. Nei cluster WASB, l'accesso OAuth non viene tentato. Potrebbero esserci molte ragioni per cui si è verificato l'errore Kerberos, ad esempio gli errori delle password non sono sincronizzati, l'account utente bloccato in Servizi di dominio Active Directory di Azure e così via. Gli hashe della password vengono sincronizzati solo quando l'utente cambia password. Quando si crea l'istanza di Servizi di dominio Active Directory di Azure, verrà avviata la sincronizzazione delle password modificate dopo la creazione. Non sincronizzerà retroattivamente le password che sono state impostate prima della sua nascita.

### <a name="resolution"></a>Risoluzione

Se ritieni che le password potrebbero non essere sincronizzate, prova a modificare la password e attendi alcuni minuti di sincronizzazione.

Provare a SSH in un è necessario provare a eseguire l'autenticazione (kinit) utilizzando le stesse credenziali utente, da un computer che fa parte del dominio. SSH nel nodo head /edge con un utente locale e quindi eseguire kinit.

---

## <a name="kinit-fails"></a>kinit fallisce

### <a name="issue"></a>Problema

Kinit fallisce.

### <a name="cause"></a>Causa

Variabile.

### <a name="resolution"></a>Risoluzione

Affinché kinit abbia successo, `sAMAccountName` è necessario conoscere il proprio (questo è il nome breve dell'account senza il regno). `sAMAccountName`è in genere il prefisso dell'account (come bob in `bob@contoso.com`). Per alcuni utenti, potrebbe essere diverso. Avrete bisogno della possibilità di sfogliare `sAMAccountName`/ cercare nella directory per imparare il vostro .

Modi per `sAMAccountName`trovare :

* Se riesci ad accedere ad Ambari utilizzando l'amministratore locale di Ambari, guarda l'elenco degli utenti.

* Se si dispone di un computer Windows aggiunto a un [dominio,](../../active-directory-domain-services/manage-domain.md)è possibile utilizzare gli strumenti standard di Windows AD per esplorare. Questa operazione richiede un account di lavoro nel dominio.

* Dal nodo head, è possibile utilizzare i comandi SAMBA per eseguire la ricerca. Questa operazione richiede una sessione Kerberos valida (kinit riuscito). net ads ricerca "(userPrincipalName

    I risultati di ricerca / `sAMAccountName` sfoglia dovrebbero mostrare l'attributo. Inoltre, si potrebbe guardare `pwdLastSet`ad `badPasswordTime` `userPrincipalName` altri attributi come , , ecc per vedere se quelle proprietà corrispondono a quello che ci si aspetta.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit non riesce con errore di preautenticazione

### <a name="issue"></a>Problema

Kinit fallisce con `Preauthentication` il fallimento.

### <a name="cause"></a>Causa

Nome utente o password non corretti.

### <a name="resolution"></a>Risoluzione

Controllare il nome utente e la password. Verificare inoltre la presenza di altre proprietà descritte in precedenza. Per abilitare il debug `export KRB5_TRACE=/tmp/krb.log` dettagliato, eseguire dalla sessione prima di provare kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Il comando Job / HDFS non riesce a causa di TokenNotFoundException

### <a name="issue"></a>Problema

Il comando Job / `TokenNotFoundException`HDFS non riesce a causa di .

### <a name="cause"></a>Causa

Il token di accesso OAuth richiesto non è stato trovato per l'esito positivo del processo/comando. Il driver ADLS / ABFS tenterà di recuperare il token di accesso OAuth dal servizio credenziali prima di effettuare richieste di archiviazione. Questo token viene registrato quando si accede al portale Ambari utilizzando lo stesso utente.

### <a name="resolution"></a>Risoluzione

Assicurarsi di aver eseguito correttamente l'accesso al portale Ambari una volta tramite il nome utente la cui identità viene utilizzata per eseguire il processo.

---

## <a name="error-fetching-access-token"></a>Errore durante il recupero del token di accesso

### <a name="issue"></a>Problema

L'utente riceve `Error fetching access token`il messaggio di errore .

### <a name="cause"></a>Causa

Questo errore si verifica in modo intermittente quando gli utenti tentano di accedere ad ADLS Gen2 utilizzando ACL e il token Kerberos è scaduto.

### <a name="resolution"></a>Risoluzione

* Per Azure Data Lake Storage Gen1, pulire nuovamente la cache del browser e accedere ad Ambari.For Azure Data Lake Storage Gen1, clean browser cache and log into Ambari again.

* Per Azure Data Lake Storage `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>` Gen2, eseguire per l'utente che l'utente sta tentando di

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
