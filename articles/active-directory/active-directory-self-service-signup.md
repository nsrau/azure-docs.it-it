---
title: Informazioni sull&quot;iscrizione self-service per Azure | Documentazione Microsoft
description: Panoramica dell&quot;iscrizione self-service per Azure e informazioni su come gestire il processo di iscrizione e come acquisire un nome di dominio DNS.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/09/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d517f434fe81617061d37a5b60f4e49806948005


---
# <a name="what-is-self-service-signup-for-azure"></a>Informazioni sull'iscrizione self-service per Azure
Questo argomento illustra il processo di iscrizione self-service e l'acquisizione della proprietà di un nome di dominio DNS.  

## <a name="why-use-self-service-signup"></a>Perché usare l'iscrizione self-service?
* Acquisire più velocemente clienti su servizi da loro richiesti.
* Creare offerte basate sulla posta elettronica per un servizio.
* Creare flussi di iscrizione basati sulla posta elettronica che rapidamente consentono agli utenti di creare identità usando i relativi alias di posta elettronica di lavoro facili da ricordare.
* Le directory di Azure non gestite possono essere trasformate in directory gestite in un secondo momento ed essere riutilizzate per altri servizi.

## <a name="terms-and-definitions"></a>Termini e definizioni
* **Iscrizione self-service**: si tratta del metodo in base al quale un utente si iscrive a un servizio cloud e ha un'identità creata automaticamente in Azure Active Directory (Azure AD) in base al dominio di posta elettronica.
* **Directory di Azure non gestita**: si tratta della directory in cui viene creata tale identità. È una directory priva di un amministratore globale.
* **Utente verificato per la posta elettronica**: si tratta di un tipo di account utente in Azure AD. Un utente che dispone di un'identità creata automaticamente a seguito dell'iscrizione per un'offerta self-service è noto come utente di posta elettronica verificato. Un utente di posta elettronica verificato è un membro regolare di una directory contrassegnata con creationmethod=EmailVerified.

## <a name="user-experience"></a>Esperienza utente
Si supponga, ad esempio, un utente il cui indirizzo di posta elettronica Dan@BellowsCollege.com riceve file riservati tramite posta elettronica. I file sono stati protetti da Azure Rights Management (Azure RMS). Tuttavia l'organizzazione di Dan, Bellows College, non è iscritta ad Azure RMS né ha distribuito Active Directory RMS. In questo caso, Dan può iscriversi con un abbonamento gratuito a RMS per utenti singoli per leggere i file protetti.

Se Dan è il primo utente con un indirizzo di posta elettronica di BellowsCollege.com a iscriversi all'offerta self-service, viene creata una directory non gestita per BellowsCollege.com in Azure AD. Se altri utenti del dominio BellowsCollege.com si iscrivono a questa offerta o a un'offerta self-service simile, avranno a loro volta account utente verificati tramite posta elettronica creati nella stessa directory non gestita in Azure.

## <a name="admin-experience"></a>Esperienza amministratore
Un amministratore che possiede il nome di dominio DNS di una directory di Azure non gestita può eseguire l'acquisizione della proprietà o l'unione della directory dopo aver dimostrato di esserne il proprietario. Nelle sezioni successive viene illustrata l'esperienza di amministratore in modo più dettagliato; in ogni caso qui di seguito è riportato un riepilogo:

* Quando si acquisisce la proprietà di una directory di Azure non gestita, si diventa semplicemente l'amministratore globale di tale directory. In alcuni casi si tratta di un'acquisizione interna.
* Quando si esegue l'unione di una directory di Azure non gestita, si aggiunge il nome di dominio DNS della directory non gestita alla directory di Azure gestita e viene creato un mapping tra utenti e risorse in modo che gli utenti possano continuare ad accedere ai servizi senza interruzioni. In alcuni casi si tratta di un'acquisizione esterna.

## <a name="what-gets-created-in-azure-active-directory"></a>Cosa viene creato in Azure Active Directory?
#### <a name="directory"></a>Directory
* Viene creata una directory di Azure Active Directory per il dominio, una singola directory per dominio.
* La directory di Azure AD è priva di un amministratore globale.

#### <a name="users"></a>Utenti
* Per ogni utente che effettua l'iscrizione viene creato un oggetto utente nella directory di Azure AD.
* Ogni oggetto utente viene contrassegnato come esterno.
* Ogni utente è autorizzato ad accedere al servizio al quale si è iscritto.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Come si richiede una directory di Azure AD self-service per un dominio di cui si è proprietari?
È possibile richiedere una directory di Azure AD self-service eseguendo la convalida del dominio. La convalida del dominio dimostra che si è proprietari del dominio mediante la creazione di record DNS.

Esistono due modi per eseguire acquisizioni della proprietà DNS di una directory di Azure AD:

* acquisizione della proprietà interna (l'amministratore individua una directory di Azure non gestita e vuole trasformarla in una directory gestita)
* acquisizione della proprietà esterna (l'amministratore prova ad aggiungere un nuovo dominio alla directory di Azure gestita)

Si può essere interessati alla convalida della proprietà personale di un dominio perché è in corso l'acquisizione della proprietà di una directory non gestita dopo l'iscrizione self-service da parte di un utente oppure per aggiungere un nuovo dominio a una directory gestita esistente. Ad esempio, si dispone di un dominio denominato contoso.com e si vuole aggiungere un nuovo dominio denominato contoso.co.uk o contoso.uk.

## <a name="what-is-domain-takeover"></a>Che cos'è l'acquisizione di dominio?
In questa sezione viene descritto come convalidare la proprietà di un dominio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>Cos'è la convalida del dominio e perché viene usata?
Per eseguire operazioni su una directory, Azure AD richiede la convalida della proprietà del dominio DNS.  La convalida del dominio consente di richiedere la directory e di alzare il livello della directory self-service a quello di una directory gestita oppure di unire la directory self-service in una directory gestita esistente.

## <a name="examples-of-domain-validation"></a>Esempi di convalida del dominio
Esistono due modi per eseguire acquisizioni della proprietà DNS di una directory:

* acquisizione della proprietà interna (ad esempio, un amministratore individua una directory self-service non gestita e vuole trasformarla in una directory gestita)
* acquisizione della proprietà esterna (ad esempio, un amministratore prova ad aggiungere un nuovo dominio a una directory gestita)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Acquisizione della proprietà interna: innalzamento del livello di una directory non gestita self-service a quello di una directory gestita
In caso di acquisizione della proprietà interna, la directory viene convertita da non gestita a gestita. È necessario completare la convalida del nome di dominio DNS, in cui si crea un record MX o un record TXT nella zona DNS. Questa azione:

* Convalida la proprietà del dominio
* Rende la directory gestita
* Rende l'utente amministratore globale della directory

Si supponga, ad esempio, che un amministratore IT di Bellows College rileva che utenti della scuola si sono iscritti alle offerte self-service. In qualità di proprietario registrato del nome DNS BellowsCollege.com, l'amministratore IT può convalidare la proprietà del nome DNS in Azure e quindi acquisire la proprietà della directory non gestita. Quest'ultima diventa quindi una directory gestita e all'amministratore IT viene assegnato il ruolo di amministratore globale per la directory BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Acquisizione della proprietà esterna: unione di una directory self-service in una directory gestita esistente
In un'acquisizione della proprietà esterna, è già presente una directory gestita e si vuole aggiungere tutti gli utenti e i gruppi di una directory non gestita a quella directory gestita, invece di possedere due directory separate.

Un amministratore di una directory gestita potrebbe aggiungere un dominio a cui è associata una directory non gestita.

Si supponga, ad esempio, di essere un amministratore IT e di avere già una directory gestita per Contoso.com, il nome di dominio registrato per la propria organizzazione. Si scopre che gli utenti dell'organizzazione hanno eseguito l'iscrizione self-service per un'offerta usando il nome di dominio di posta elettronica user@contoso.co.uk,, ovvero un altro nome di dominio dell'organizzazione. Attualmente gli utenti hanno account in una directory contoso.co.uk non gestita.

Non si vogliono gestire due directory separate, quindi si unisce la directory non gestita per contoso.co.uk alla directory contoso.com esistente gestita dal reparto IT.

L'acquisizione esterna segue lo stesso processo di convalida DNS dell'acquisizione interna.  La differenza risiede nel fatto che utenti e servizi vengono rimappati alla directory gestita dal reparto IT.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>Qual è l'impatto dell'esecuzione di un'acquisizione esterna?
Con un'acquisizione esterna viene creato un mapping di utenti a risorse in modo che gli utenti possono continuare ad accedere ai servizi senza interruzioni. Molte applicazioni, tra cui RMS per utenti singoli, gestiscono anche il mapping degli utenti alle risorse e gli utenti possono continuare ad accedere a questi servizi senza alcuna modifica. Se un'applicazione non gestisce il mapping degli utenti alle risorse in modo efficace, l'acquisizione esterna potrebbe essere bloccata in modo esplicito per impedire agli utenti un'esperienza deludente.

#### <a name="directory-takeover-support-by-service"></a>Supporto dell'acquisizione della proprietà di directory in base al servizio
I servizi seguenti supportano attualmente l'acquisizione:

* RMS

L'acquisizione verrà presto supportata dai servizi seguenti:

* PowerBI

I servizi seguenti non la supportano e richiedono un'ulteriore azione da parte dell'amministratore per la migrazione dei dati utente dopo un'acquisizione esterna.

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Come eseguire un'acquisizione del nome di dominio DNS
Sono disponibili alcune opzioni per la modalità di esecuzione della convalida di un dominio (ed eseguire un'acquisizione se lo si desidera):

1. Portale di gestione di Azure

   Un'acquisizione viene attivata eseguendo l'aggiunta di un dominio.  Se esiste già una directory per il dominio, è possibile eseguire un'acquisizione della proprietà esterna.

   Accedere al portale di Azure mediante le proprie credenziali.  Passare alla directory esistente e quindi ad **Aggiungi dominio**.
2. Office 365

   È possibile usare le opzioni sulla pagina [Gestisci domini](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) in Office 365 per lavorare con i domini e i record DNS. Vedere [Verificare il dominio in Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).
3. Windows PowerShell

   I passaggi seguenti sono necessari per eseguire una convalida mediante Windows PowerShell.

   | Passaggio | Cmdlet da usare |
   | --- | --- |
   | Creare un oggetto credential |Get-Credential |
   | Connessione ad Azure AD |Connect-MsolService |
   | Ottenere un elenco di domini |Get-MsolDomain |
   | Creare una richiesta di verifica |Get-MsolDomainVerificationDns |
   | Creare record DNS |Eseguire questa operazione sul server DNS |
   | Verificare la richiesta di verifica |Confirm-MsolEmailVerifiedDomain |

Ad esempio:

1. Connettersi ad Azure AD usando le credenziali usate per rispondere all'offerta self-service:

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. Ottenere un elenco di domini:

    Get-MsolDomain
3. Eseguire quindi il cmdlet Get-MsolDomainVerificationDns per creare una richiesta di verifica:

    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord

    ad esempio:

    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
4. Copiare il valore (la richiesta di verifica) restituita da questo comando.

    ad esempio:

    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. Nello spazio dei nomi DNS pubblico creare un record txt DNS che contiene il valore copiato nel passaggio precedente.

    Il nome per questo record è quello del dominio padre, pertanto se si crea questo record di risorse usando il ruolo DNS di Windows Server, lasciare il nome record vuoto e incollare solo il valore nella casella di testo
6. Eseguire il cmdlet Confirm-MsolDomain per verificare la richiesta di verifica:

    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*

    ad esempio:

    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

Una richiesta di verifica riporta al prompt senza errori.

## <a name="how-do-i-control-self-service-settings"></a>Come controllare le impostazioni di self-service?
Gli amministratori attualmente dispongono di due controlli self-service. Possono controllare:

* Se gli utenti possono essere aggiunti alla directory tramite posta elettronica.
* Se gli utenti possono ottenere autonomamente una licenza per applicazioni e servizi.

### <a name="how-can-i-control-these-capabilities"></a>Come è possibile controllare queste funzionalità?
Un amministratore può configurare queste funzionalità usando questi parametri del cmdlet Set-MsolCompanySettings di Azure AD:

* **AllowEmailVerifiedUsers** controlla se un utente può essere aggiunto a una directory non gestita o crearne una. Se tale parametro è impostato su $false, nessun utente verificato tramite posta elettronica può essere aggiunto alla directory.
* **AllowAdHocSubscriptions** controlla la possibilità per gli utenti di eseguire l'iscrizione self-service. Se tale parametro è impostato su $false, nessun utente può eseguire l'iscrizione self-service.

### <a name="how-do-the-controls-work-together"></a>Come operano congiuntamente i controlli?
Questi due parametri possono essere usati insieme per definire un controllo più preciso sull'iscrizione self-service. Ad esempio, il comando seguente consentirà agli utenti di eseguire l'iscrizione self-service, ma solo se tali utenti dispongono già di un account in Azure AD (in altre parole, gli utenti, per i quali sarebbe necessario che venisse creato un account di posta elettronica verificato, non possono eseguire l'iscrizione self-service):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

Il diagramma di flusso seguente illustra tutte le diverse combinazioni di questi parametri e le condizioni risultanti per la directory e l'iscrizione self-service.

![][1]

Per ulteriori informazioni ed esempi su come usare questi parametri, vedere [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Vedere anche
* [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Informazioni di riferimento sui cmdlet di Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png



<!--HONumber=Feb17_HO2-->


