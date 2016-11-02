<properties
    pageTitle="Panoramica di Azure MFA | Microsoft Azure"
    description="Cos'è Azure Multi-Factor Authentication (MFA) e perché usare questo servizio, informazioni sul client Multi-Factor Authentication e sui diversi metodi e versioni disponibili. "
    keywords="Introduzione a MFA, panoramica di mfa, che cos'è mfa"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>


# <a name="what-is-azure-multi-factor-authentication?"></a>Informazioni su Azure Multi-Factor Authentication
La verifica in due passaggi è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. In genere richiede due o più dei metodi di verifica seguenti:

- Un'informazione nota (in genere una password)
- Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
- Una caratteristica fisica dell'utente (biometrica)

<center>![Nome utente e password](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Certificati](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smartphone](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smart card](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smart card virtuale](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Nome utente e password](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) è una soluzione di verifica in due passaggi di Microsoft. Azure MFA consente di proteggere l'accesso ai dati e alle applicazioni dell'utente, garantendo al tempo stesso una procedura di accesso semplice. Offre autenticazione avanzata tramite una gamma di metodi di verifica, fra cui una telefonata, un SMS o una verifica dell'app per dispositivi mobili.

>[AZURE.VIDEO multi-factor-authentication-overview]

## <a name="why-use-azure-multi-factor-authentication?"></a>Vantaggi dell'uso di Azure Multi-Factor Authentication

Oggi più che mai le persone sono sempre più connesse. Grazie a smartphone, tablet, portatili e computer, gli utenti possono contare su diverse opzioni per scegliere come connettersi e restare connessi in qualsiasi momento. Le persone possono accedere ai propri account e alle applicazioni da qualsiasi luogo e questo significa poter svolgere più attività e servire meglio i clienti.

Azure multi-Factor Authentication è una soluzione semplice da usare, scalabile e affidabile che offre un secondo metodo di autenticazione in modo che gli utenti siano sempre protetti.

![Facile da usare](./media/multi-factor-authentication/simple.png)| ![Scalabile](./media/multi-factor-authentication/scalable.png)| ![Sempre protetti](./media/multi-factor-authentication/protected.png)|![Affidabile](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Facile da usare**|**Scalabile**|**Sempre protetti**|**Affidabile**

- **Facile da usare** : Azure Multi-Factor Authentication è semplice da configurare e usare. La protezione aggiuntiva offerta da Azure Multi-Factor Authentication consente agli utenti di usare e gestire i propri dispositivi. L'aspetto più importante è che in molti casi può essere configurata con pochi semplici clic.
- **Scalabile** : Azure Multi-Factor Authentication usa le potenzialità del cloud e si integra con Active Directory locale e con le app personalizzate. Questa protezione viene estesa anche agli scenari di importanza strategica con volumi elevati.
- **Sempre protetti** : Azure multi-Factor Authentication fornisce autenticazione avanzata grazie all'uso dei più elevati standard di settore.
- **Affidabile** : la disponibilità di Azure Multi-Factor Authentication è garantita al 99,9%. Il servizio viene considerato non disponibile quando non è in grado di ricevere o elaborare le richieste di verifica per la verifica in due passaggi.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## <a name="how-azure-multi-factor-authentication-works"></a>Come funziona Azure Multi-Factor Authentication

La sicurezza della verifica in due passaggi sta nel suo approccio a livelli. Compromettere più metodi di verifica è infatti più difficile per gli autori degli attacchi. Anche se un utente malintenzionato riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Se l'utente smarrisce il dispositivo, la persona che lo trova non potrà usarlo a meno che non ne conosca la password.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## <a name="methods-available-for-multi-factor-authentication"></a>Metodi disponibili per Multi-Factor Authentication
Quando un utente effettua l'accesso, gli viene inviata una richiesta di verifica aggiuntiva. Di seguito è riportato un elenco di metodi che possono essere utilizzati per la seconda verifica.

Metodo di verifica | Descrizione
------------- | ------------- |
Chiamata telefonica | Viene effettuata una chiamata al telefono dell'utente chiedendogli di verificare che stia effettuando l'accesso. Premendo il tasto # verrà completato il processo di verifica. Questa opzione è configurabile e può essere modificata con un codice specificato dall'utente.
SMS | Viene inviato un SMS allo smartphone dell'utente con un codice di 6 cifre. Immettere il codice per completare il processo di verifica.
Notifica dell'app per dispositivi mobili | Viene inviata una richiesta allo smartphone dell'utente chiedendogli di completare la verifica selezionando **Verifica** dall'app per dispositivi mobili. Questa operazione viene eseguita se la notifica dell'app è il metodo di verifica primario. Se l'utente riceve la richiesta quando non sta effettuando l'accesso, potrà segnalare la frode.
Codice di verifica dell'app per dispositivi mobili | L'app per dispositivi mobili sul dispositivo dell'utente genera un codice di verifica. Questa operazione viene eseguita se è stato selezionato un codice di verifica come metodo di verifica principale.

Per i metodi di verifica delle app per dispositivi mobili, Azure Multi-Factor Authentication opera con app di autenticazione di terze parti per smartphone. Si consiglia l'uso dell'app Microsoft Authenticator, disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versioni disponibili di Azure Multi-Factor Authentication
Azure Multi-Factor Authentication è disponibile in tre diverse versioni. 

Versione | Descrizione
------------- | ------------- |
Multi-Factor Authentication per Office 365 | Questa versione funziona solo con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono quindi proteggere le risorse di Office 365 con la verifica in due passaggi. Questa versione viene fornita con una sottoscrizione a Office 365.
Multi-Factor Authentication per amministratori di Azure | Lo stesso sottoinsieme di funzionalità di verifica in due passaggi per Office 365 è disponibile gratuitamente per tutti gli amministratori di Azure. Ogni account amministrativo di una sottoscrizione di Azure può abilitare questa funzionalità per una protezione aggiuntiva. Un amministratore che voglia accedere al portale di Azure per creare una macchina virtuale, un sito Web, gestire l'archiviazione o usare qualsiasi altro servizio di Azure può aggiungere Multi-Factor Authentication all'account di amministratore.
Azure Multi-Factor Authentication | Azure multi-Factor Authentication offre la più ampia gamma di funzionalità. Mette a disposizione opzioni di configurazione aggiuntive tramite il [portale di Azure classico](http://manage.windowsazure.com), segnalazione avanzata e il supporto per un intervallo di applicazioni locali e cloud. Azure Multi-Factor Authentication fa parte di Azure Active Directory Premium ed Enterprise Mobility Suite e può essere distribuito sul cloud o localmente.

## <a name="feature-comparison-of-versions"></a>Confronto tra le funzionalità delle versioni
La tabella seguente indica un elenco delle funzionalità disponibili nelle varie versioni di Azure Multi-Factor Authentication.

>[AZURE.NOTE] Questa tabella di confronto descrive le funzionalità incluse in ogni sottoscrizione. Se si dispone di Azure AD Premium o Enterprise Mobility Suite alcune funzionalità potrebbero non essere disponibili a seconda che si usi [MFA nel cloud o MFA in locale](multi-factor-authentication-get-started.md).

Funzionalità | Multi-Factor Authentication per Office 365 (incluso nelle SKU di Office 365)|Multi-Factor Authentication per amministratori di Azure (incluso con una sottoscrizione Azure) | Azure Multi-Factor Authentication (incluso in Azure AD Premium ed Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Gli amministratori possono proteggere gli account tramite Multi-Factor Authentication| ● | ● (disponibile solo per gli account amministratore di Azure)|●
App per dispositivi mobili come secondo fattore|● | ● | ●
Chiamata telefonica come secondo fattore|● | ● | ●
SMS come secondo fattore|● | ● | ●
Password di app per i client che non supportano MFA|● | ● | ●
Controllo amministrazione sui metodi di autenticazione| ● | ● | ●
Modalità PIN| | | ●
Avviso di illecito| | | ●
Report MFA| | | ●
Bypass monouso| | | ●
Messaggi di saluto personalizzati per le telefonate| | | ●
Personalizzazione dell'ID chiamante per le telefonate| | | ●
Conferma evento| | | ●
IP attendibili| | | ●
Memorizzazione di MFA per dispositivi attendibili |● | ● | ●
SDK MFA | | | ● richiede un provider di Multi-Factor Authentication e una sottoscrizione completa di Azure
MFA per applicazioni locali che utilizzano il server MFA| | | ●

## <a name="how-to-get-azure-multi-factor-authentication"></a>Come ottenere Azure Multi-Factor Authentication

Se si desidera avere a disposizione tutte le funzionalità di Azure Multi-Factor Authentication, ci sono varie opzioni:

1.  Acquistare le licenze di Azure Multi-Factor Authentication e assegnarle agli utenti.
2.  Acquistare licenze con Azure Multi-Factor Authentication incluso nel pacchetto, ad esempio Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite e assegnarle agli utenti.
3.  Creare un provider Azure Multi-Factor Authentication all'interno di una sottoscrizione di Azure. Quando si usa un provider di Azure Multi-Factor Authentication, sono disponibili due modelli di uso fatturati tramite la sottoscrizione di Azure:  
    - **Per utente**. Per le aziende che vogliono abilitare la verifica in due passaggi per un numero fisso di dipendenti che richiedono regolarmente l'autenticazione.  
    - **Per autenticazione**. Per le aziende che vogliono abilitare la verifica in due passaggi per un gruppo esteso di utenti esterni che non richiedono regolarmente l'autenticazione.  

Azure multi-Factor Authentication fornisce metodi di verifica selezionabili per cloud e server. Ciò significa che è possibile scegliere i metodi da rendere disponibili agli utenti. Questa funzionalità è attualmente in anteprima pubblica per la versione cloud dell'autenticazione a più fattori. Per altre informazioni, vedere i [metodi di verifica selezionabili](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Per altre informazioni sui prezzi, vedere [Prezzi - Multi-Factor Authentication (MFA).](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure Multi-Factor Authentication, il primo passaggio consiste nello [scegliere tra MFA nel cloud o locale](multi-factor-authentication-get-started.md)



<!--HONumber=Oct16_HO2-->


