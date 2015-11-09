<properties
   pageTitle="Prerequisiti per Azure Active Directory Connect | Microsoft Azure"
   description="Article description that will be displayed on landing pages and in most search results"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor="curtand"/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Prerequisiti per Azure Active Directory Connect (Azure AD Connect)
Questo argomento descrive i prerequisiti e i requisiti hardware per Azure AD Connect.

## Prima di installare Azure AD Connect
Prima di installare Azure AD Connect, sono necessari alcuni elementi.

**Azure AD**

- Sottoscrizione di Azure o [sottoscrizione di una versione di valutazione di Azure](http://azure.microsoft.com/pricing/free-trial/). È necessaria solo per l'accesso al portale di Azure, non per l'uso di Azure AD Connect. Se si usa PowerShell o Office 365, non è necessaria una sottoscrizione di Azure per usare Azure AD Connect. Se si dispone di una licenza di Office 365, è inoltre possibile usare il portale di Office 365. Con una licenza di Office 365 a pagamento è inoltre possibile accedere al portale di Azure dal portale di Office 365.
- Verificare il dominio che si prevede di usare in Azure AD. Ad esempio, se si prevede di usare contoso.com per gli utenti, assicurarsi che il dominio sia stato verificato e che non si usi solo il dominio predefinito contoso.onmicrosoft.com.
- Una directory di Azure AD consentirà, per impostazione predefinita, 50.000 oggetti. Quando si verifica il dominio, il limite viene aumentato a 300.000 oggetti. Se sono necessari anche più oggetti in Azure AD, è necessario aprire un caso di supporto per aumentare ulteriormente il limite. Se sono necessari più di 500.000 oggetti, è necessaria una licenza, ad esempio Office 365, Azure AD Basic, Azure AD Premium o Enterprise Mobility Suite.

**Ambiente e server locali**

- Il livello funzionale della foresta e della versione dello schema di Active Directory deve essere Windows Server 2003 o versione successiva. I controller di dominio possono eseguire qualsiasi versione, purché siano soddisfatti i requisiti del livello dello schema e della foresta.
- Azure AD Connect deve essere installato in Windows Server 2008 o versione successiva. Questo server può essere un controller di dominio o un server membro, se si usano le impostazioni rapide. Se si usano le impostazioni personalizzate, il server può anche essere autonomo e non deve essere aggiunto a un dominio.
- Se si prevede di usare la funzionalità di sincronizzazione delle password, il server deve essere in Windows Server 2008 R2 SP1 o versione successiva.
- Se viene distribuito Active Directory Federation Services, i server in cui verrà installato ADFS o il proxy dell'applicazione Web devono essere Windows Server 2012 R2 o versione successiva. In tali server per l'installazione remota è necessario che sia abilitata Gestione remota Windows.
- Per archiviare i dati sull'identità, Azure AD Connect richiede un database SQL. Per impostazione predefinita, viene installato SQL Server 2012 Express LocalDB (una versione ridotta di SQL Server Express) e viene creato l'account del servizio nel computer locale. SQL Server Express ha un limite di dimensioni di 10 GB che consente di gestire circa 100.000 oggetti. Se è necessario gestire un numero di oggetti directory maggiore, si dovrà installare una versione di SQL Server diversa. Azure AD Connect supporta tutte le versioni di Microsoft SQL Server da SQL Server 2008 (con SP4) a SQL Server 2014.

**Account**

- Un account amministratore globale di Azure AD per la directory di Azure AD con cui si desidera eseguire l'integrazione.
- Un account amministratore dell'organizzazione per Active Directory locale se si usano le impostazioni rapide o l’aggiornamento da DirSync.
- [Account Active Directory](active-directory-aadconnect-accounts-permissions.md) se si usa il percorso di installazione delle impostazioni personalizzate.

**Connettività**

- Se si usa un proxy in uscita per la connessione a Internet, è necessario aggiungere al file l'impostazione seguente **C:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config** per l'installazione guidata e la sincronizzazione di Azure AD deve essere in grado di connettersi a Internet e Azure AD.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Questo testo deve essere immesso alla fine del file. In questo codice, &lt;PROXYADRESS&gt; rappresenta il nome host o l'indirizzo IP proxy effettivo. - Se il proxy limita gli URL a cui è possibile accedere, gli URL documentati in [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/it-IT/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) devono essere aperti nel proxy.

**Altri**

- Facoltativo: un account utente di prova per verificare la sincronizzazione.

## Prerequisiti dei componenti

Azure AD Connect si basa su PowerShell e .NET 4.5.1. In base alla versione di Windows Server, seguire questa procedura:


- Windows Server 2012 R2
  - PowerShell viene installato per impostazione predefinita, non è richiesta alcuna azione.
  - .NET 4.5.1 e versioni successive sono disponibili tramite Windows Update. Assicurarsi di aver installato gli aggiornamenti più recenti per Windows Server nel Pannello di controllo.
- Windows Server 2008 R2 e Windows Server 2012
  - La versione più recente di PowerShell è disponibile in **Windows Management Framework 4.0**, disponibile nell'[Area download Microsoft](http://www.microsoft.com/downloads).
  - .NET 4.5.1 e versioni successive sono disponibili nell'[Area download Microsoft](http://www.microsoft.com/downloads).
- Windows Server 2008
  - La versione più recente supportata di PowerShell è disponibile in **Windows Management Framework 3.0**, disponibile nell'[Area download Microsoft](http://www.microsoft.com/downloads).
 - .NET 4.5.1 e versioni successive sono disponibili nell'[Area download Microsoft](http://www.microsoft.com/downloads).

## Componenti di supporto di Azure AD Connect

Di seguito è riportato un elenco di componenti che verranno installati da Azure AD Connect nel server in cui è installato Azure AD Connect. L'elenco riguarda un'istallazione di SQL Express di base. Se si sceglie di usare una versione diversa di SQL Server nella pagina Installazione dei servizi di sincronizzazione, SQL Express LocalDB non viene installato in locale.

- Utilità della riga di comando di Microsoft SQL Server 2012
- Client nativo di Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Modulo di Azure Active Directory per Windows PowerShell
- Assistente per l'accesso a Microsoft Online Services per professionisti IT
- Microsoft Visual C++ 2013 Redistribution Package


## Requisiti hardware per Azure AD Connect
La tabella seguente indica i requisiti minimi di sincronizzazione di Azure AD Connect per il computer.

| Numero di oggetti in Active Directory | CPU | Memoria | Dimensioni del disco rigido |
| ------------------------------------- | --- | ------ | --------------- |
| Meno di 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10\.000-50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50\.000-100.000 | 1,6 GHz | 16 GB | 100 GB |
| Per 100.000 o più oggetti, è necessaria la versione completa di SQL Server| | | |
| 100\.000-300.000 | 1,6 GHz | 32 GB | 300 GB |
| 300\.000-600.000 | 1,6 GHz | 32 GB | 450 GB |
| Più di 600.000 | 1,6 GHz | 32 GB | 500 GB |

I requisiti minimi per i computer che eseguono ADFS o i server applicazioni Web sono i seguenti:

- CPU: dual core da 1,6 GHz o superiore
- MEMORIA: 2 GB o superiore
- Macchina virtuale di Azure: configurazione A2 o superiore


## Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO1-->