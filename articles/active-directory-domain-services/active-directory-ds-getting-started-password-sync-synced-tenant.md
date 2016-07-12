<properties
	pageTitle="Servizi di dominio Azure AD: Abilitare la sincronizzazione password | Microsoft Azure"
	description="Introduzione a Servizi di dominio Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/06/2016"
	ms.author="maheshu"/>

# Passaggio successivo *(anteprima)*: Abilitare la sincronizzazione password in Servizi di dominio Azure AD.

## Attività 5: Abilitare la sincronizzazione password in Servizi di dominio Azure AD per un tenant di Azure AD sincronizzato
Dopo aver abilitato Servizi di dominio Azure AD per la directory di Azure AD, l'attività successiva consiste nell'abilitare la sincronizzazione delle password in Servizi di dominio Azure AD. Ciò consente agli utenti di accedere al dominio usando le credenziali aziendali.

I passaggi sono diversi a seconda che l'organizzazione abbia una directory di Azure AD solo cloud o sia impostata per la sincronizzazione con la directory locale tramite Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Directory di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)
- [Directory di Azure AD sincronizzata](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Tenant sincronizzati - Abilita la sincronizzazione di hash di credenziali NTLM e Kerberos in Azure AD
Se il tenant di Azure AD per l'organizzazione è impostato per la sincronizzazione con la directory locale tramite Azure AD Connect, sarà necessario configurare Azure AD Connect per sincronizzare gli hash delle credenziali necessari per l'autenticazione NTLM e Kerberos. Questi hash non sono sincronizzati con Azure AD per impostazione predefinita e la procedura seguente consentirà di abilitare la sincronizzazione degli hash nel tenant di Azure AD.

#### Installare o aggiornare Azure AD Connect

Sarà necessario installare l’ultima versione consigliata a livello generale di Azure AD Connect in un computer aggiunto a un dominio. Se si dispone di un'istanza esistente del programma di installazione di Azure AD Connect, sarà necessario aggiornarla per usare la build disponibile a livello generale di Azure AD Connect. Accertarsi di usare la versione più recente di Azure AD Connect, per evitare problemi o bug noti che potrebbero essere già stati corretti.

**[Scaricare Azure AD Connect](http://www.microsoft.com/download/details.aspx?id=47594)**

Versione consigliata: **1.1.189.0**, pubblicata il 3 giugno 2016.

  > [AZURE.WARNING] L'installazione dell'ultima versione consigliata di Azure AD Connect è NECESSARIA per abilitare le credenziali di password legacy (obbligatorio per l'autenticazione NTLM e Kerberos) da sincronizzare nel tenant di Azure AD. Questa funzionalità non è disponibile nelle versioni precedenti di Azure AD Connect o con lo strumento DirSync legacy.

Le istruzioni per l'installazione di Azure AD Connect sono disponibili nell'articolo [Introduzione ad Azure AD Connect](../active-directory/active-directory-aadconnect.md).


#### Forzare la sincronizzazione password in Azure AD

Per forzare la sincronizzazione password completa e abilitare la sincronizzazione degli hash delle password per tutti gli utenti (inclusi gli hash delle credenziali necessari per l'autenticazione NTLM o Kerberos) nel tenant di Azure AD, eseguire lo script di PowerShell seguente in ogni foresta di Active Directory.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

A seconda delle dimensioni della directory (numero di utenti, gruppi e così via), la sincronizzazione delle credenziali in Azure AD richiederà del tempo. Le password saranno utilizzabili nel dominio gestito dei servizi di dominio Azure Active Directory non appena le hash di credenziali saranno sincronizzate con Azure.


<br>

## Contenuti correlati

- [Abilitare la sincronizzazione password in Servizi di dominio Azure AD per una directory di Azure AD solo cloud](active-directory-ds-getting-started-password-sync.md)

- [Amministrare un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Aggiungere una macchina virtuale Windows a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Aggiungere una macchina virtuale Red Hat Enterprise Linux a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-rhel-linux-vm.md)

<!---HONumber=AcomDC_0706_2016-->