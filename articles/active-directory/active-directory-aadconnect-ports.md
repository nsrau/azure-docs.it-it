<properties
	pageTitle="Azure AD Connect: porte | Microsoft Azure"
	description="Questa pagina è una pagina di riferimento tecnico relativa alle porte che devono essere aperte per Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="billmath"/>  

# Porte e protocolli necessari per la soluzione ibrida di gestione delle identità
Il documento seguente è un riferimento tecnico per fornire informazioni sulle porte e i protocolli necessari per l'implementazione di una soluzione ibrida di gestione delle identità. Usare la figura riportata di seguito e vedere la tabella corrispondente.

![Cos'è Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)  

## Tabella 1 - Azure AD Connect e AD locale
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e l'AD locale.

Protocol | Porte | Descrizione
--------- | --------- |---------
DNS|53 (TCP/UDP)| Ricerche DNS della foresta di destinazione.
Kerberos|88 (TCP/UDP)| Autenticazione Kerberos per la foresta Active Directory.
MS-RPC |135 (TCP/UDP)| Usato durante la configurazione iniziale della procedura guidata Azure AD Connect quando si esegue l'associazione alla foresta Active Directory,
LDAP|389 (TCP/UDP)| Usato per l'importazione di dati da Active Directory. I dati vengono crittografati con la firma e il sigillo Kerberos.
LDAP/SSL|636 (TCP/UDP)| Usato per l'importazione di dati da Active Directory. Il trasferimento dati è firmato e crittografato. Usato solo se si utilizza SSL.
RPC |49152- 65535 (porta RPC elevata casuale)(TCP/UDP)| Usato durante la configurazione iniziale di Azure AD Connect quando si esegue l'associazione alla foresta Active Directory. Per altre informazioni, vedere gli articoli [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) e [KB224196](https://support.microsoft.com/kb/224196).

## Tabella 2 - Azure AD Connect e AD locale
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e Azure AD.

Protocol |Porte |Descrizione
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usato per il download di CRL (Certificate Revocation List) per verificare i certificati SSL.
HTTPS|443 (TCP/UDP)| Usato per la sincronizzazione con Azure AD.

Per un elenco degli URL e degli indirizzi IP da aprire nel firewall, vedere [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## Tabella 3 - Azure AD Connect e server federativi/WAP
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra il server Azure AD Connect e i server federativi/WAP.

Protocol |Porte |Descrizione
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Usato per il download di CRL (Certificate Revocation List) per verificare i certificati SSL.
HTTPS|443 (TCP/UDP)| Usato per la sincronizzazione con Azure AD.
WinRM|5985| Listener di Gestione remota Windows

## Tabella 4 - server federativi e WAP
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra i server federativi e i server WAP.

Protocol |Porte |Descrizione
--------- | --------- |---------
HTTPS|443 (TCP/UDP)| Usato per l'autenticazione.

## Tabella 5 - WAP e utenti
Questa tabella descrive le porte e i protocolli necessari per la comunicazione tra gli utenti e i server WAP.

Protocol |Porte |Descrizione
--------- | --------- |--------- |
HTTPS|443 (TCP/UDP)| Usato per l'autenticazione del dispositivo.
TCP|49443 (TCP)| Usato per l'autenticazione del certificato.

## Tabella 6a e 6b - Agente di Azure AD Connect Health per (AD FS/sincronizzazione) e Azure AD
Le tabelle seguenti descrivono gli endpoint, le porte e i protocolli necessari per la comunicazione tra gli agenti di Azure AD Connect Health e Azure AD

### Tabella 6a - Agente di Azure AD Connect Health per (AD FS/sincronizzazione) e Azure AD
Questa tabella descrive le porte in uscita seguenti e i protocolli necessari per la comunicazione tra gli agenti di Azure AD Connect Health e Azure AD.

Protocol |Porte |Descrizione
--------- | --------- |--------- |
HTTPS|443 (TCP/UDP)| In uscita
Bus di servizio di Azure|5671 (TCP/UDP)| In uscita

### 6b - Endpoint per l'agente di Azure AD Connect Health per (AD FS/sincronizzazione) e Azure AD
Per un elenco di endpoint vedere la sezione [Requisiti in Installazione dell'agente di Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md#requirements).

<!---HONumber=AcomDC_0831_2016-->