---
title: Applicazioni con carattere jolly in Azure Active Directory Application Proxy | Microsoft Docs
description: Informazioni su come usare le applicazioni con carattere jolly in Azure Active Directory Application Proxy.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 28e43d48b4fa27202d58ee081a60e2fb5bfe9d99
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Applicazioni con carattere jolly in Azure Active Directory Application Proxy 

In Azure Active Directory (Azure AD) la configurazione di un numero elevato di applicazioni locali può risultare difficile da gestire e introdurre il rischio di errori di configurazione nel caso in cui molte applicazioni richiedano le stesse impostazioni. [Azure AD Application Proxy](active-directory-application-proxy-get-started.md) consente di risolvere questo problema con la pubblicazione di applicazioni con carattere jolly per gestire numerose applicazioni in una sola volta. Questa soluzione consente di:

-   Semplificare il carico amministrativo.
-   Ridurre il numero di potenziali errori di configurazione.
-   Accedere in modo sicuro ad altre risorse.

Questo articolo offre le informazioni necessarie per configurare la pubblicazione di applicazioni con carattere jolly nell'ambiente in uso.


## <a name="create-a-wildcard-application"></a>Creare un'applicazione con carattere jolly 

È possibile creare un'applicazione con carattere jolly (*) se è presente un gruppo di applicazioni con la stessa configurazione. Potenziali candidati per la creazione di un'applicazione con carattere jolly sono le applicazioni che condividono le impostazioni seguenti:

- Il gruppo di utenti che possono accedere a tali applicazioni
- Il metodo SSO
- Il protocollo di accesso (http, https)

È possibile pubblicare applicazioni con caratteri jolly se il formato degli URL sia interni che esterni è il seguente:

> http(s)://*.\<domain\>

Ad esempio: `http(s)://*.adventure-works.com`. Anche se gli URL interni ed esterni possono usare domini diversi, la procedura consigliata è quella di usare lo stesso dominio. Quando si pubblica l'applicazione, viene visualizzato un errore se uno degli URL non contiene un carattere jolly.

Se sono presenti altre applicazioni con impostazioni di configurazione diverse, è necessario pubblicare queste eccezioni come applicazioni separate per sovrascrivere i valori predefiniti impostati per il carattere jolly. Le applicazioni senza carattere jolly hanno sempre la precedenza sulle applicazioni con carattere jolly. Dal punto di vista della configurazione, si tratta di applicazioni normali.

La creazione di un'applicazione con carattere jolly è basata sullo stesso [flusso di pubblicazione](application-proxy-publish-azure-portal.md) disponibile per tutte le altre applicazioni. L'unica differenza è che viene incluso un carattere jolly negli URL e potenzialmente nella configurazione SSO.


## <a name="prerequisites"></a>Prerequisiti

### <a name="custom-domains"></a>Domini personalizzati

Mentre i [domini personalizzati](active-directory-application-proxy-custom-domains.md) sono facoltativi per tutte le altre applicazioni, per le applicazioni con carattere jolly costituiscono un prerequisito. Per creare domini personalizzati è necessario:

1. Creare un dominio verificato all'interno di Azure. 
2. Caricare un certificato SSL in formato PFX nel proxy dell'applicazione.

Può essere opportuno usare un certificato con carattere jolly corrispondente all'applicazione che si intende creare. In alternativa, è anche possibile usare un certificato contenente un elenco di applicazioni specifiche. In questo caso, solo le applicazioni elencate nel certificato saranno accessibili tramite l'applicazione con carattere jolly.

Per motivi di sicurezza, questo requisito è obbligatorio e non saranno supportati caratteri jolly per le applicazioni che non possono usare un dominio personalizzato per l'URL esterno.

### <a name="dns-updates"></a>Aggiornamenti del DNS

Quando si usano i domini personalizzati, è necessario creare una voce DNS con un record CNAME per l'URL esterno (ad esempio, `*.adventure-works.com`) che punti all'URL esterno dell'endpoint proxy dell'applicazione. Per le applicazioni con carattere jolly, il record CNAME deve puntare agli URL esterni pertinenti:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Per verificare la corretta configurazione del record CNAME, è possibile usare [nslookup](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/nslookup) su uno degli endpoint di destinazione, ad esempio `expenses.adventure-works.com`.  La risposta deve includere l'alias già menzionato (`<Id.tenant>.runtime.msappproxy.net`).


## <a name="considerations"></a>Considerazioni


### <a name="accepted-formats"></a>Formati accettati

Per le applicazioni con carattere jolly, il campo **URL interno** deve contenere un valore con formato `http(s)://*.<domain>`. 

![AppId](./media/active-directory-application-proxy-wildcard\22.png)


Quando si configura un valore nel campo **URL esterno**, è necessario usare il formato seguente: `https://*.<custom domain>` 

![AppId](./media/active-directory-application-proxy-wildcard\21.png)

Altre posizioni del carattere jolly, altre stringhe regex o caratteri jolly multipli non sono supportati e possono causare errori.


### <a name="excluding-applications-from-the-wildcard"></a>Esclusione di applicazioni dall'applicazione con carattere jolly

Per escludere un'applicazione da un'applicazione con carattere jolly, è possibile:

- Pubblicare l'applicazione di eccezione come applicazione normale. 
- Abilitare il carattere jolly solo per applicazioni specifiche tramite le impostazioni DNS.  


La pubblicazione di un'applicazione come applicazione normale è il metodo preferito per escludere un'applicazione da un'applicazione con carattere jolly. È necessario pubblicare le applicazioni escluse prima delle applicazioni con carattere jolly per garantire che le eccezioni vengano applicate fin dall'inizio. L'applicazione più specifica avrà sempre la precedenza. Un'applicazione pubblicata come `budgets.finance.adventure-works.com` ha la precedenza sull'applicazione `*.finance.adventure-works.com`, che a sua volta ha la precedenza sull'applicazione `*.adventure-works.com`. 

È anche possibile limitare l'uso del carattere jolly ad applicazioni specifiche tramite la gestione del DNS. Come procedura consigliata, è necessario creare una voce CNAME contenente un carattere jolly e corrispondente al formato dell'URL esterno configurato. Tuttavia, è possibile in alternativa puntare gli URL di applicazioni specifiche sui caratteri jolly. Ad esempio, anziché `*.adventure-works.com`, puntare `hr.adventure-works.com`, `expenses.adventure-works.com` e `travel.adventure-works.com individually` su `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`. 

Se si usa questa opzione, è necessaria anche un'altra voce CNAME per il valore `AppId.domain`, ad esempio `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, che punti sullo stesso percorso. È possibile trovare il valore **AppId** nella pagina delle proprietà dell'applicazione con carattere jolly:

![AppId](./media/active-directory-application-proxy-wildcard\01.png)



### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>Impostazione dell'URL della home page del pannello App

L'applicazione con carattere jolly è rappresentata con un solo riquadro nel [pannello App](https://myapps.microsoft.com). Per impostazione predefinita, questo riquadro è nascosto. Per visualizzare il riquadro e consentire agli utenti di accedere a una pagina specifica:

1. Seguire le linee guida per l'[impostazione dell'URL di una home page](application-proxy-office365-app-launcher.md).
2. Impostare **Show Application** (Mostra applicazione) su **true** nella pagina delle proprietà dell'applicazione.

### <a name="kerberos-constrained-delegation"></a>Delega vincolata Kerberos

Per le applicazioni che usano la [delega vincolata Kerberos (KCD) come metodo SSO](active-directory-application-proxy-sso-using-kcd.md) può essere necessario un carattere jolly anche per il nome dell'entità servizio elencato per il metodo SSO. Ad esempio, il nome dell'entità servizio può essere `HTTP/*.adventure-works.com`. È comunque necessario configurare i singoli nomi dell'entità servizio nei server back-end (ad esempio, `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).



## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Applicazione con carattere jolly generica

In questo scenario sono presenti tre diverse applicazioni da pubblicare:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Tutte e tre le applicazioni:

- Vengono usate da tutti gli utenti.
- Usano *Autenticazione integrata di Windows*. 
- Hanno le stesse proprietà.


Per pubblicare l'applicazione con carattere jolly, è possibile usare la procedura illustrata in [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md). Questo scenario presuppone che:

- Sia presente un tenant con l'ID seguente: `000aa000-11b1-2ccc-d333-4444eee4444e` 

- Sia stato configurato un dominio verificato con nome `adventure-works.com`

- Sia stata creata una voce **CNAME** che punta `*.adventure-works.com` su `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`

Seguendo le [procedure descritte](application-proxy-publish-azure-portal.md) è possibile creare un nuovo proxy dell'applicazione nel tenant. In questo esempio il carattere jolly è presente nei campi seguenti:

- URL interno:

    ![URL interno](./media/active-directory-application-proxy-wildcard\42.png)


- URL esterno:

    ![URL esterno](./media/active-directory-application-proxy-wildcard\43.png)

 
- Nome dell'entità servizio dell'applicazione interna: 

    ![Configurazione del nome dell'entità servizio](./media/active-directory-application-proxy-wildcard\44.png)


Con la pubblicazione dell'applicazione con carattere jolly è ora possibile accedere alle tre applicazioni passando agli URL che si conoscono (ad esempio, `travel.adventure-works.com`).

La configurazione implementa la struttura seguente:

![AppId](./media/active-directory-application-proxy-wildcard\05.png)

| Colore | Descrizione |
| ---   | ---         |
| Blu  | Applicazioni pubblicate in modo esplicito e visibili nel portale di Azure. |
| Grigio  | Applicazioni accessibili tramite l'applicazione padre. |




## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Applicazione con carattere jolly generica con un'eccezione

In questo scenario, oltre alle tre applicazioni generiche, è presente un'altra applicazione, `finance.adventure-works.com`, che deve essere accessibile solo dal reparto finanziario. Con la struttura corrente, l'applicazione finanziaria è accessibile da tutti i dipendenti attraverso l'applicazione con carattere jolly. Per modificare questa impostazione, è necessario escludere l'applicazione dall'applicazione con carattere jolly configurando l'applicazione finanziaria come separata e dotata di autorizzazioni più restrittive.



È necessario verificare l'esistenza di un record CNAME che punti `finance.adventure-works.com` sull'endpoint specifico dell'applicazione, specificato nella pagina relativa al proxy dell'applicazione. Per questo scenario, `finance.adventure-works.com` punta a `https://finance-awcycles.msappproxy.net/`. 

Secondo le [procedure descritte](application-proxy-publish-azure-portal.md), questo scenario richiede le impostazioni seguenti:


- Nel campo **URL interno** è necessario impostare **finance** anziché un carattere jolly. 

    ![URL interno](./media/active-directory-application-proxy-wildcard\52.png)

- Nel campo **URL esterno** è necessario impostare **finance** anziché un carattere jolly. 

    ![URL esterno](./media/active-directory-application-proxy-wildcard\53.png)

- Nel campo Nome dell'entità servizio dell'applicazione interna è necessario impostare **finance** anziché un carattere jolly.

    ![Configurazione del nome dell'entità servizio](./media/active-directory-application-proxy-wildcard\54.png)


Questa configurazione implementa lo scenario seguente:

![Scenario](./media/active-directory-application-proxy-wildcard\09.png)

Poiché `finance.adventure-works.com` è un URL più specifico di `*.adventure-works.com`, ha la precedenza. Gli utenti che accedono a `finance.adventure-works.com` sperimentano l'applicazione in base alle impostazioni specificate in Finance Resources. In questo caso, solo i dipendenti del reparto finanziario possono accedere a `finance.adventure-works.com`.

Se sono state pubblicate più applicazioni per il dominio finance e `finance.adventure-works.com` è un dominio verificato, è possibile pubblicare un'altra applicazione con carattere jolly `*.finance.adventure-works.com`. Poiché si tratta di un'applicazione più specifica rispetto alla generica `*.adventure-works.com`, ha la precedenza se un utente accede a un'applicazione del dominio finance.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su:

- **Domini personalizzati**, vedere [Utilizzo di domini personalizzati nel Proxy di applicazione AD Azure](active-directory-application-proxy-custom-domains.md).

- **Pubblicazione di applicazioni**, vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](application-proxy-publish-azure-portal.md).


