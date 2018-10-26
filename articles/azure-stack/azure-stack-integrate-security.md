---
title: Inoltro syslog di Azure Stack
description: Descrive come integrare Azure Stack con soluzioni di monitoraggio utilizzando l'inoltro di syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 10/23/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: d81478e6bdaf4a1844d01278b961350c81b2edd6
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087730"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integrazione di Data Center di Azure Stack - l'inoltro di syslog

Questo articolo illustra come usare syslog per l'integrazione dell'infrastruttura di Azure Stack con soluzioni di protezione esterne già distribuite nel Data Center. Ad esempio, un sistema di gestione di eventi informazioni di sicurezza (SIEM). Il canale di syslog espone a controlli, avvisi e registri di protezione da tutti i componenti dell'infrastruttura di Azure Stack. Usare l'inoltro syslog per l'integrazione con soluzioni di monitoraggio di sicurezza e/o per recuperare tutti i controlli, avvisi e la sicurezza i log per l'archiviazione per il mantenimento dati. 

Inizia con l'aggiornamento 1809, Azure Stack è un client integrato syslog che, una volta configurata, vengono generati messaggi di syslog con il payload in formato CEF (Common Event Format).

Il diagramma seguente illustra l'integrazione di Azure Stack con un sistema SIEM esterni. Sono disponibili due modelli di integrazione che devono essere presi in considerazione: la prima di tutto uno (quello in blu) è l'infrastruttura di Azure Stack che comprende le macchine virtuali dell'infrastruttura e i nodi Hyper-V. Tutti i controlli, i log di sicurezza e gli avvisi provenienti da tali componenti in modo centralizzato raccolti e vengono esposte tramite syslog con payload CEF. Il modello di integrazione è descritto in questa pagina del documento.
Il secondo modello di integrazione è quello in arancione e illustra i baseboard management controller (BMC), l'host del ciclo di vita dell'hardware (HLH), le macchine virtuali e/o Appliance virtuali che eseguono il partner hardware di monitoraggio e gestione software e le proprietà di commutatori rack (TOR). Poiché questi componenti sono partner di hardware specifici, contattare il proprio partner hardware per la documentazione su come integrarli con un sistema SIEM esterni.

![Diagramma di inoltro di Syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>La configurazione dell'inoltro di syslog

Il client di syslog in Azure Stack supporta le configurazioni seguenti:

1. **Syslog tramite TCP, con l'autenticazione reciproca (client e server) e la crittografia TLS 1.2:** In questa configurazione sia al server syslog nel client di syslog può verificare l'identità reciproca tramite certificati. I messaggi vengono inviati tramite un canale crittografato TLS 1.2.

2. **Syslog su TCP con l'autenticazione server e la crittografia TLS 1.2:** In questa configurazione, il client di syslog possa verificare l'identità del server syslog tramite un certificato. I messaggi vengono inviati tramite un canale crittografato TLS 1.2.

3. **Syslog tramite TCP, senza crittografia:** In questa configurazione syslog client né il server syslog verifica l'identità di loro. I messaggi vengono inviati in testo non crittografato tramite TCP.

4. **Syslog su UDP, senza crittografia:** In questa configurazione syslog client né il server syslog verifica l'identità di loro. I messaggi vengono inviati in testo non crittografato tramite UDP.

> [!IMPORTANT]
> Microsoft consiglia di usare TCP usando l'autenticazione e crittografia (configurazione #1 o al requisito minimo, #2) per gli ambienti di produzione per la protezione da attacchi man-in-the-middle e intercettazione dei messaggi.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdlet per configurare l'inoltro di syslog
La configurazione dell'inoltro di syslog richiede l'accesso all'endpoint con privilegi (PEP). Sono state aggiunte due cmdlet di PowerShell per PEP per configurare l'inoltro syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parametri di cmdlet

I parametri per *Set-SyslogServer* cmdlet:

| Parametro | DESCRIZIONE | type | Obbligatorio |
|---------|---------|---------|---------|
|*ServerName* | FQDN o indirizzo IP del server syslog | string | Sì|
|*ServerPort* | Numero di porta al server syslog è in ascolto sulla | string | Sì|
|*Crittografia*| Forza il client invia i messaggi syslog in testo non crittografato | Flag | no|
|*SkipCertificateCheck*| Ignorare la convalida del certificato fornito dal server syslog durante l'handshake TLS iniziale | Flag | no|
|*SkipCNCheck*| Ignorare la convalida del valore nome comune del certificato fornito dal server syslog durante l'handshake TLS iniziale | Flag | no|
|*UseUDP*| Usare syslog con UDP come protocollo di trasporto |Flag | no|
|*Rimuovi*| Rimuove la configurazione del server dai client e arrestare l'inoltro di syslog| Flag | no|

I parametri per *Set-SyslogClient* cmdlet:
| Parametro | DESCRIZIONE | type |
|---------|---------| ---------|
| *pfxBinary* | file PFX contenente il certificato utilizzato dal client come identità per l'autenticazione con il server syslog  | Byte[] |
| *CertPassword* |  Password per importare la chiave privata associata con il file pfx | SecureString |
|*RemoveCertificate* | Rimuovere certificati da client | Flag|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configurare l'inoltro di syslog con TCP, l'autenticazione reciproca e la crittografia TLS 1.2

In questa configurazione, il client di syslog in Azure Stack inoltra i messaggi al server syslog tramite TCP, con la crittografia TLS 1.2. Durante l'handshake iniziale, il client verifica che il server fornisce un certificato valido e attendibile; Analogamente, il client fornisce anche un certificato al server come prova dell'identità. Questa configurazione è la più sicura che fornisce una convalida completa dell'identità del client e il server e i messaggi vengono inviati tramite un canale crittografato. 

> [!IMPORTANT]
> Microsoft consiglia di usare questa configurazione per gli ambienti di produzione. 

Per configurare l'inoltro di syslog con TCP, l'autenticazione reciproca e la crittografia TLS 1.2, eseguire questi due cmdlet in una sessione PEP:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Il certificato client deve avere la stessa radice specificato durante la distribuzione di Azure Stack. Deve inoltre contenere una chiave privata.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configurare l'inoltro di syslog con TCP, l'autenticazione Server e la crittografia TLS 1.2

In questa configurazione, il client di syslog in Azure Stack inoltra i messaggi al server syslog tramite TCP, con la crittografia TLS 1.2. Durante l'handshake iniziale, il client verifica inoltre che il server fornisce un certificato valido e attendibile. In questo modo il client per inviare messaggi a destinazioni non attendibili.
TCP usando l'autenticazione e crittografia è la configurazione predefinita e rappresenta il livello minimo di sicurezza consigliati da Microsoft per un ambiente di produzione. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Nel caso in cui si desidera testare l'integrazione del server syslog con il client di Azure Stack usando un certificato autofirmato e/o non attendibile, è possibile utilizzare questi flag per ignorare la convalida del server eseguita dal client durante l'handshake iniziale.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft ne sconsiglia l'uso del flag - SkipCertificateCheck per gli ambienti di produzione. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configurare l'inoltro di syslog con TCP e Nessuna crittografia

In questa configurazione, il client di syslog in Azure Stack inoltra i messaggi al server syslog tramite TCP, senza crittografia. Il client non verifica l'identità del server né fornisce la propria identità al server per la verifica. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Microsoft sconsiglia l'utilizzo di questa configurazione per ambienti di produzione. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>La configurazione dell'inoltro di syslog con UDP e Nessuna crittografia

In questa configurazione, il client di syslog in Azure Stack inoltra i messaggi al server syslog tramite UDP, senza crittografia. Il client non verifica l'identità del server né fornisce la propria identità al server per la verifica. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Sebbene sia più semplice da configurare UDP senza crittografia, non fornisce alcuna protezione contro gli attacchi man-in-the-middle e intercettazione dei messaggi. 

> [!IMPORTANT]
> Microsoft sconsiglia l'utilizzo di questa configurazione per ambienti di produzione. 


## <a name="removing-syslog-forwarding-configuration"></a>Rimozione di configurazione di inoltro di syslog

Per rimuovere completamente la configurazione del server syslog e arrestare l'inoltro di syslog:

**Rimuovere la configurazione del server syslog dal client**

```PowerShell  
Set-SyslogServer -Remove
```

**Rimuovere il certificato client dal client**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Verifica per determinare se la configurazione di syslog

Se il client di syslog connesso al server syslog, è consigliabile iniziare presto la ricezione di eventi. Se non viene visualizzato qualsiasi evento, verificare la configurazione del client di syslog, eseguendo i cmdlet seguenti:

**Verificare la configurazione del server nel client di syslog**

```PowerShell  
Get-SyslogServer
```

**Verificare l'installazione del certificato nel client di syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schema del messaggio Syslog

L'inoltro di syslog dell'infrastruttura di Azure Stack invia messaggi formattati in comune con Event Format (CEF).
Ogni messaggio syslog è strutturato basati su questo schema: 

```Syslog
<Time> <Host> <CEF payload>
```

Il payload CEF è basato sulla struttura seguente, ma il mapping per ogni campo varia a seconda del tipo di messaggio (eventi di Windows, avviso creato, avviso chiuso).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Mapping di CEF per gli eventi degli endpoint con privilegi

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Tabella degli eventi per l'endpoint con privilegi:

| Event | ID evento PEP | Nome dell'attività PEP | Gravità |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |da 1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|

Tabella livelli di gravità PEP:

| Gravità | Level | Valore numerico |
|----------|-------| ----------------|
|0|Undefined|Valore: 0. Indica i log a tutti i livelli|
|10|Critico|Valore: 1. Indica i log per un avviso critico|
|8|Tipi di errore| Valore: 2. Indica i log per un errore|
|5|Avviso|Valore: 3. Indica i log per un messaggio di avviso|
|2|Informazioni|Valore: 4. Indica i log per un messaggio informativo|
|0|Dettagliato|Valore: 5. Indica i log a tutti i livelli|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Mapping di CEF per gli eventi degli endpoint di ripristino

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Tabella degli eventi per l'endpoint di ripristino:

| Event | ID evento REP | Nome dell'attività REP | Gravità |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tabella REP Severity:
| Gravità | Level | Valore numerico |
|----------|-------| ----------------|
|0|Undefined|Valore: 0. Indica i log a tutti i livelli|
|10|Critico|Valore: 1. Indica i log per un avviso critico|
|8|Tipi di errore| Valore: 2. Indica i log per un errore|
|5|Avviso|Valore: 3. Indica i log per un messaggio di avviso|
|2|Informazioni|Valore: 4. Indica i log per un messaggio informativo|
|0|Dettagliato|Valore: 5. Indica i log a tutti i livelli|

### <a name="cef-mapping-for-windows-events"></a>Mapping di CEF per gli eventi di Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabella livelli di gravità per gli eventi di Windows:
| Valore di gravità CEF | Livello evento Windows | Valore numerico |
|--------------------|---------------------| ----------------|
|0|Undefined|Valore: 0. Indica i log a tutti i livelli|
|10|Critico|Valore: 1. Indica i log per un avviso critico|
|8|Tipi di errore| Valore: 2. Indica i log per un errore|
|5|Avviso|Valore: 3. Indica i log per un messaggio di avviso|
|2|Informazioni|Valore: 4. Indica i log per un messaggio informativo|
|0|Dettagliato|Valore: 5. Indica i log a tutti i livelli|

Tabella di estensione personalizzata per gli eventi di Windows in Azure Stack:
| Nome dell'estensione personalizzata | Esempio di evento di Windows | 
|-----------------------|---------|
|MasChannel | Sistema|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0. EseDiskFlushConsistency!! ESENT, FACEVANO SÌ!! 0x800000|
|MasEventDescription| Le impostazioni di criteri di gruppo per l'utente sono state elaborate correttamente. Non sono presenti modifiche rilevate dopo l'ultimo corretta elaborazione dei criteri di gruppo.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Controllo riuscito|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<SID di Windows\> |
|MasTask |0|
|MasTaskCategory| Creazione del processo|
|MasUserData|KB4093112!! 5112!! Installato!! 0x0!! WindowsUpdateAgent Xpath: Organizzazione di eventi / / UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapping di CEF per gli avvisi creati

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabella livelli di gravità degli avvisi:
| Gravità | Level |
|----------|-------|
|0|Undefined|
|10|Critico|
|5|Avviso|

Tabella di estensione personalizzata per gli avvisi creati in Azure Stack:
| Nome dell'estensione personalizzata | Esempio | 
|-----------------------|---------|
|MasEventDescription|Descrizione: Un account \<TestUser\> è stato creato per \<DominioVerifica\>. È un potenziale rischio di sicurezza. : Correzione: contattare il supporto tecnico. È necessaria un'assistenza clienti per risolvere il problema. Non tentare di risolvere il problema senza l'assistenza fornita. Prima di aprire una richiesta di supporto, avviare il processo di raccolta di file di log usando le indicazioni fornite dalla https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mapping di CEF per gli avvisi chiusi

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

L'esempio seguente mostra un messaggio syslog con payload CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Passaggi successivi

[Criteri di manutenzione](azure-stack-servicing-policy.md)