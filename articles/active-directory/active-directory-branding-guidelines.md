<properties
   pageTitle="Linee guida sulla personalizzazione delle applicazioni | Microsoft Azure"
   description="Guida completa alle risorse rivolte agli sviluppatori per Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin"/>


# Linee guida sulla personalizzazione per le applicazioni


Questo argomento descrive le linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory. Queste linee guida consentiranno agli sviluppatori di fornire ai clienti le indicazioni più appropriate quando questi desiderano usare l'account aziendale o dell'istituto di istruzione, gestito in Azure AD, per iscriversi e accedere all'applicazione.

## Confronto tra account Microsoft personali e aziendali o dell'istituto di istruzione

Microsoft gestisce due tipi di account utente:

- **Account personali** (noti in precedenza come Windows Live ID). Rappresentano la relazione tra utenti *individuali* e Microsoft e vengono usati per accedere a dispositivi di consumo e servizi forniti da Microsoft. Questi account sono concepiti per un uso personale.

- **Account aziendali o dell'istituto di istruzione.** Questi account sono gestiti da Microsoft per conto delle organizzazioni che usano Azure Active Directory. Vengono usati per accedere a Office 365 e altri servizi aziendali forniti da Microsoft.

Gli account Microsoft aziendali o dell'istituto di istruzione vengono normalmente assegnati agli utenti finali (dipendenti, studenti, impiegati pubblici) dalle relative organizzazioni (azienda, istituto di istruzione, agenzia governativa). Questi account vengono gestiti direttamente nel cloud, nella piattaforma Azure AD o sincronizzati con Azure AD da una directory locale, ad esempio Windows Server Active Directory. Microsoft è *responsabile* degli account aziendali o dell'istituto di istruzione, che sono tuttavia di proprietà e controllati dall'organizzazione.

## Riferimento agli account Azure AD nella propria applicazione

Microsoft non mostra agli utenti finali il nome del marchio Azure o Active Directory, né dovrebbero farlo gli sviluppatori.

- Dopo che gli utenti hanno eseguito l'accesso, è invece consigliabile visualizzare il nome e il logo dell'organizzazione il più estesamente possibile. Questa soluzione è preferibile rispetto all'uso di termini generici come "organizzazione".

- Quando gli utenti non hanno eseguito l'accesso, è consigliabile fare riferimento ai loro account come "Account aziendali o dell'istituto di istruzione" e usare il logo Microsoft per indicare che sono account gestiti da Microsoft. Non usare termini quali "account dell'azienda", "account dell'impresa" o "account della società", perché potrebbero creare confusione nell'utente.

## Pittogramma dell'account utente
In una versione precedente di queste linee guida si è consigliato di usare un pittogramma di "badge blu". In base ai commenti e suggerimenti degli utenti e degli sviluppatori, ora si consiglia invece di usare il logo Microsoft. In questo modo sarà più facile per gli utenti comprendere che possono riutilizzare l'account che usano con Office 365 o altri servizi commerciali Microsoft per accedere all'app.

## Iscrizione e accesso con Azure AD

Le app degli sviluppatori possono presentare percorsi separati per l'iscrizione e l'accesso. Nelle sezioni seguenti vengono fornite indicazioni visive per entrambi gli scenari.

**Se l'app supporta l'iscrizione da parte dell'utente (ad esempio, per una versione di valutazione gratuita o per il modello freemium)**: è possibile visualizzare un pulsante di **accesso** per consentire agli utenti di accedere all'app con il proprio account aziendale o dell'istituto di istruzione fornito da Microsoft. La prima volta che l'utente accede all'app, Azure AD visualizzerà una richiesta di consenso.

**Se l'app richiede autorizzazioni che solo gli amministratori possono concedere oppure se l'app richiede una licenza dell'organizzazione**: è consigliabile separare l'acquisizione amministrativa dall'accesso utente. Il **pulsante per "ottenere l'app"** reindirizzerà gli amministratori alla pagina di accesso, quindi verrà richiesto di concedere il consenso per conto degli utenti dell'organizzazione. Questo approccio offre il vantaggio di eliminare le richieste di consenso agli utenti finali per l'accesso all'app.

## Indicazioni visive per l'acquisizione di app

Il collegamento per "ottenere l'app" deve reindirizzare l'utente alla pagina di Azure AD per concedere (autorizzare) l'accesso, in modo che l'amministratore di un'organizzazione possa autorizzare l'app perché possa accedere ai dati dell'organizzazione ospitati da Microsoft. Verranno fornite informazioni dettagliate su come richiedere l'accesso nell’articolo [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

Dopo avere concesso il consenso all'app, gli amministratori possono scegliere di aggiungerla all'esperienza di avvio delle app di Office 365 dei propri utenti (accessibile dall'icona di avvio delle app e da [https://portal.office.com/myapps](https://portal.office.com/myapps)). Se si vuole annunciare questa funzionalità, è possibile usare termini come "Aggiungere questa app alla propria organizzazione" e visualizzare un pulsante simile al seguente:

![Scenari e tipi di applicazione](./media/active-directory-branding-guidelines/add-to-my-org.png)

È tuttavia consigliabile scrivere un testo descrittivo, invece di fare affidamento sui pulsanti. Ad esempio:
> *Se si usa già Office 365 o un altro servizio aziendale di Microsoft, è possibile concedere semplicemente <your_app_name> l'accesso ai dati dell'organizzazione. In questo modo gli utenti potranno accedere <your_app_name> con gli account aziendali esistenti.*


## Indicazioni visive per l'accesso
Nell'app dovrà essere visualizzato un pulsante di accesso che indirizza gli utenti all'endpoint di accesso corrispondente al protocollo usato per l'integrazione con Azure AD. La sezione seguente illustra in dettaglio come dovrebbe apparire questo pulsante.

### Pittogramma e "account aziendale o dell'istituto di istruzione"
È l'associazione del logo Microsoft e dei termini generici "aziendale o dell'istituto di istruzione" che distingue in modo univoco Azure AD dagli altri provider di identità eventualmente supportati dall'app. Se non è disponibile spazio sufficiente per "account aziendale o dell'istituto di istruzione", è possibile usare la dicitura abbreviata "account aziendale".

![Scenari e tipi di applicazione](./media/active-directory-branding-guidelines/work-or-school-account.png)

![Scenari e tipi di applicazione](./media/active-directory-branding-guidelines/work-account.png)

È anche possibile fornire spiegazioni aggiuntive per permettere agli utenti finali di comprendere se possono usare questo pulsante:

![Scenari e tipi di applicazione](./media/active-directory-branding-guidelines/work-account-with-explaination.png)

## Azioni consentite e non consentite per la personalizzazione
**USARE** "account aziendale o dell'istituto di istruzione" in combinazione con il logo Microsoft per rappresentare gli accessi con Azure AD. Se lo spazio disponibile è limitato, usare "account aziendale", ma **NON USARE** altri termini quali "account dell'azienda", "account dell'impresa" o "account della società".

**NON USARE** "ID di Office 365" o "ID di Azure". Office 365 è anche il nome di un'offerta Microsoft per i consumatori che non prevede l'uso di Azure AD per l'autenticazione.

**NON MODIFICARE** il logo Microsoft.

**NON ESPORRE** le personalizzazioni di Azure o Active Directory agli utenti finali. È tuttavia accettabile usare questi termini con sviluppatori, professionisti IT e amministratori.

## Azioni consentite e non consentite per la navigazione

**FORNIRE** agli utenti un modo per disconnettersi e passare a un altro account utente. Quasi tutte le persone hanno in genere un singolo account per Microsoft, Facebook, Google e Twitter, ma sono spesso associate a più organizzazioni. Il supporto per l'accesso di più utenti sarà presto disponibile.

## Supporto degli account Azure AD e Microsoft nell'app

Se l'app supporta entrambi gli account Azure AD e Microsoft, è necessario includere nell'app due pulsanti di accesso separati. Microsoft è attivamente impegnata nella realizzazione di un aggiornamento che consentirà di eseguire l'integrazione una sola volta e di supportare gli account Microsoft personale e aziendale. Quando l'aggiornamento sarà disponibile, si potrà mostrare nell'app un singolo pulsante di "accesso a Microsoft".

<!---HONumber=Sept15_HO4-->