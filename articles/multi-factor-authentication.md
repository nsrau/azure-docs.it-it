<properties  linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<h1>Informazioni su Azure Multi-Factor Authentication</h1>


L'autenticazione a due o più fattori è un metodo di autenticazione che richiede più di un metodo di verifica e con il quale viene aggiunto un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. In genere richiede due o più dei metodi di verifica seguenti:

* Un'informazione nota (in genere una password)
* Un oggetto che si possiede (un dispositivo attendibile non facile da
  duplicare, ad esempio un telefono)
* Una caratteristica fisica dell'utente (biometrica)

La sicurezza dell'autenticazione a più fattori si fonda sull'approccio basato su livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del dispositivo attendibile. Al contrario, se l'utente smarrisce il dispositivo, colui che lo trova non potrà utilizzarlo a meno che non ne conosca anche la password. Azure Multi-Factor Authentication è il servizio di autenticazione a più fattori che richiede agli utenti l'ulteriore verifica dell'accesso tramite un'app mobile, una chiamata telefonica o un SMS. È disponibile per l'utilizzo con Azure Active Directory per la protezione delle risorse locali con il server Azure Multi-Factor Authentication e con applicazioni e directory personalizzate che utilizzano l'SDK.

![Azure Multi-Factor
Authentication](./media/multi-factor-authentication/WAMFA1.png)

<h3>Protezione di Azure Active Directory su cloud</h3>


Se si abilita Multi-Factor Authentication per le identità di Azure Active Directory, agli utenti verrà richiesto di configurare una verifica aggiuntiva all'accesso successivo. È possibile utilizzare Multi-Factor Authentication per garantire la sicurezza dell'accesso ad Azure, ai Microsoft Online Services quali Office 365 e Dynamics CRM Online, nonché a servizi cloud di terze parti che integrano Azure AD senza alcuna configurazione aggiuntiva. L'autenticazione a più fattori può essere abilitata in modo rapido per un numero elevato di utenti e applicazioni a livello globale. [Ulteriori informazioni][1]

<h3>Protezione di risorse locali e Active Directory</h3>


È possibile abilitare Multi-Factor Authentication per le risorse locali,
ad esempio IIS e Active Directory tramite il server Azure Multi-Factor Authentication. Il server Azure Multi-Factor Authentication consente all'amministratore di effettuare l'integrazione con l'autenticazione IIS per proteggere applicazioni Web Microsoft IIS, autenticazione RADIUS, autenticazione LDAP e autenticazione Windows. [Ulteriori informazioni][2]

<h3>Protezione di applicazioni personalizzate</h3>


Un SDK consente l'integrazione diretta con i servizi cloud. È possibile inserire la verifica di autenticazione attiva tramite chiamata telefonica ed SMS nella procedura di accesso o nei processi relativi alle transazioni dell'applicazione e sfruttare il database utente esistente dell'applicazione. [Ulteriori informazioni][3]

<h3>Multi-Factor Authentication per Office 365</h3>


L'autenticazione a più fattori Office 365, con tecnologia Azure Multi-Factor Authentication, funziona esclusivamente con le applicazioni di Office 365 e viene gestita dal portale di Office 365. Gli amministratori possono quindi proteggere le risorse di Office 365 con l'autenticazione a più fattori. [Ulteriori informazioni][4]

<h3>Multi-Factor Authentication per amministratori di Azure</h3>


Lo stesso subset di funzionalità di Multi-Factor Authentication per Office 365 sarà disponibile per tutti gli amministratori di Azure senza costi aggiuntivi. Ogni account amministrativo della sottoscrizione di Azure può ora usufruire di un ulteriore livello di protezione abilitando la funzionalità di autenticazione a più fattori di base. Ora, un amministratore che desidera accedere al portale di Azure per creare una macchina virtuale, un sito Web, gestire l'archiviazione, i servizi mobili o qualsiasi altro servizio di Azure può aggiungere l'autenticazione a più fattori all'account di amministratore. [Ulteriori informazioni][5]

<h3>Confronto delle funzionalità di Multi-Factor Authentication</h3>


Di seguito sono descritte le versioni di Multi-Factor Authentication disponibili con un breve riepilogo delle funzionalità offerte. È possibile farvi riferimento per determinare la versione di Multi-Factor Authentication più adatta in base alle esigenze. [Ulteriori informazioni][5]

![Confronto delle funzionalità di Azure Multi-Factor
Authentication](./media/multi-factor-authentication/mfacomparison1.png)

**Risorse aggiuntive**

* [Iscrizione ad Azure come
  organizzazione](/en-us/manage/services/identity/organizational-account/)
* [Identità di Azure](/en-us/manage/windows/fundamentals/identity/)
* [Libreria di Azure Multi-Factor Authentication][6]



[1]: http://msdn.microsoft.com/it-it/library/dn249466.aspx
[2]: http://msdn.microsoft.com/it-it/library/dn249467.aspx
[3]: http://msdn.microsoft.com/it-it/library/dn249464.aspx
[4]: http://msdn.microsoft.com/it-it/library/dn383636.aspx
[5]: http://msdn.microsoft.com/it-it/library/dn249471.aspx
[6]: http://technet.microsoft.com/en-us/library/dn249471.aspx