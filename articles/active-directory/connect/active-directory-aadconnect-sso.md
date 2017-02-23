---
title: 'Azure AD Connect: Single Sign-On | Documentazione Microsoft'
description: Questo argomento contiene le indicazioni necessarie sull&quot;opzione Single Sign-On da un&quot;istanza di Active Directory (AD) locale a un&quot;istanza di Azure Active Directory basata sul cloud (Azure AD) e servizi associati.
services: active-directory
keywords: "che cos&quot;è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: a268907eea2862ae2d054f30accfd4d771a7d880
ms.openlocfilehash: ae97e66b8fb0992550c5a4f1f8418c5cb7e496b5

---

# <a name="what-is-single-sign-on-sso-preview"></a>Che cos'è Single Sign-On (SSO) (anteprima)
Single Sign-On è un'opzione che può essere abilitata in Azure Active Directory Connect tramite [sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md) o [autenticazione pass-through](active-directory-aadconnect-pass-through-authentication.md). Quando è abilitata, consente agli utenti di non dover digitare la password, ma solo il nome utente per accedere ad Azure Active Directory (Azure AD) o ad altri servizi cloud da computer aziendali e se connessi alla rete aziendale.

![Single sign-on](./media/active-directory-aadconnect-sso/sso1.png)

Permettendo ai propri utenti finali di usufruire dell'SSO, l'accesso ai servizi basati sul cloud diventa più familiare. Inoltre, tale opzione offre all'organizzazione un processo semplice e sicuro che non richiede componenti locali aggiuntivi.

SSO è una funzionalità che viene abilitata tramite Azure AD Connect e funziona con la sincronizzazione delle password o l'autenticazione pass-through e Active Directory locale. Per usare Single Sign-On nell'ambiente interessato, è necessario assicurarsi che gli utenti finali:

- Si trovino su un computer aggiunto a un dominio
- Dispongano di una connessione diretta a un controller di dominio, ad esempio nella rete aziendale cablata o wireless o tramite una connessione di accesso remoto, ad esempio una connessione VPN.
- Definiscano gli endpoint di Kerberos nel cloud come parte dell'area Intranet del browser.

In assenza di uno qualsiasi di questi elementi, ad esempio se il computer è esterno alla rete aziendale, all'utente viene semplicemente richiesto di immettere la password come da procedura senza Single Sign-On.

## <a name="supported-clients"></a>Client supportati
Single Sign-On è supportato tramite i client basati su Web browser e i client di Office che prevedono l'[autenticazione moderna](https://aka.ms/modernauthga) nei computer che supportano l'autenticazione Kerberos, ad esempio Windows. La matrice seguente fornisce i dettagli dei client basati su browser su vari sistemi operativi.

| SO\Browser |Internet Explorer|Chrome|Firefox|Edge
| --- | --- |--- | --- | --- |
|Windows 10|Sì|Sì|Sì*|No
|Windows 8.1|Sì|Sì|Sì*|N/D
|Windows 8|Sì|Sì|Sì*|N/D
|Windows 7|Sì|Sì|Sì*|N/D
|Mac|N/D|N/D|N/D|N/D

\*Richiede la configurazione separata.

>[!NOTE]
>Per i client basati su Windows 10, si consiglia di usare [Aggiunta ad Azure AD](../active-directory-azureadjoin-overview.md) per ottenere risultati ottimali con Azure AD.

## <a name="how-single-sign-on-works"></a>Funzionamento di Single Sign-On

Quando si abilita Single Sign-On in Azure AD Connect, viene creato un account computer denominato AZUREADSSOACCT in Active Directory locale e la chiave di decrittografia di Kerberos è condivisa in modo sicuro con Azure AD. Inoltre, vengono creati due nomi di entità servizio (SPN) Kerberos per rappresentare gli URL del cloud usati durante l'autenticazione tra il client e Azure AD.

Al termine dell'installazione, il processo di autenticazione sarà lo stesso di qualsiasi altra applicazione basata su Autenticazione integrata di Windows (IWA). Se si ha familiarità con il funzionamento dell'autenticazione integrata di Windows, allora si conosce già la modalità di funzionamento di Single Sign-On con Azure AD. In caso contrario, di seguito è descritto il processo di autenticazione integrata di Windows:

![Single sign-on](./media/active-directory-aadconnect-sso/sso2.png)

Innanzitutto, l'utente tenta di accedere a una risorsa che considera attendibili i token emessi da Azure AD, ad esempio SharePoint Online. SharePoint Online reindirizza a quel punto l'utente per l'autenticazione con Azure AD. L'utente indica quindi il proprio nome utente in modo che Azure AD possa stabilire se sia abilitato il Single-Sign-On per la sua organizzazione. Supponendo che l'accesso Single-Sign-On sia abilitato per l'organizzazione, si verifica quanto segue.

1.    Azure AD richiede al client, tramite una risposta di tipo 401 Non autorizzato, di fornire un ticket Kerberos.
2.    Il client richiede un ticket da Active Directory per Azure AD.
3.    Active Directory individua l'account del computer creato da Azure AD Connect e restituisce un ticket Kerberos al client, crittografato con il segreto dell'account del computer. Il ticket include l'identità dell'utente attualmente connesso al computer.
4.    Il client invia il ticket Kerberos acquisito da Active Directory ad Azure AD.
5.    Azure AD decrittografa il ticket Kerberos utilizzando la chiave già condivisa, quindi restituisce un token all'utente o chiede all'utente di fornire prove aggiuntive, ad esempio l'autenticazione a più fattori come richiesto dalla risorsa.

Single Sign-On è una funzionalità opportunistica, il che significa che, in caso di errore, l'utente deve solo immettere la password nella pagina di accesso, come di consueto.

## <a name="enabling-sso-with-pass-through-authentication-or-password-sync"></a>Abilitazione di SSO con autenticazione pass-through o sincronizzazione delle password
Azure AD Connect offre un semplice processo per abilitare Single Sign-On con l'autenticazione pass-through o la sincronizzazione delle password. È necessario assicurarsi di disporre di diritti di amministratore per uno dei domini all'interno di ogni foresta sincronizzata per consentire la configurazione dei nomi di entità servizio (SPN) Kerberos sull'account del computer. Il nome utente e la password non vengono archiviati in Azure AD Connect o Azure AD e vengono usati solo per questa operazione.

Quando si installa Azure AD Connect, selezionare un'installazione personalizzata in modo da poter selezionare l'opzione Single Sign-On nella pagina di accesso dell'utente. Per altre informazioni, vedere [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Single sign-on](./media/active-directory-aadconnect-sso/sso3.png)

Dopo aver abilitato Single Sign-On, è possibile continuare la procedura guidata dell'installazione finché non viene visualizzata la pagina Single Sign-On.

![Single sign-on](./media/active-directory-aadconnect-sso/sso4.png)

Per ogni foresta indicata, specificare i dettagli dell'account appropriato e Single Sign-On verrà abilitato per la directory di Azure.

>[!NOTE]
>Azure AD Connect deve essere in grado di comunicare con \*.msappproxy.net sulla porta 9090 (TCP) per configurare SSO. L'operazione di apertura della porta è necessaria solo durante la configurazione e non durante le autenticazioni degli utenti finali.

## <a name="ensuring-clients-sign-in-automatically"></a>Garantire l'accesso automatico dei client
Per impostazione predefinita, i browser non tentano di inviare le credenziali ai server Web, a meno che l'URL non venga definito come interno all'area Intranet. In genere il browser può calcolare l'area corretta esaminando l'URL. Ad esempio, se l'URL è http://intranet/, il browser invia automaticamente le credenziali non appena esegue il mapping dell'URL all'area Intranet. Tuttavia, se l'URL contiene un punto, ad esempio http://intranet.contoso.com/, il computer non invia automaticamente le credenziali e considera l'URL come un sito Internet.

Poiché gli URL usati per single sign-on in Azure AD contengono un punto, devono essere aggiunti esplicitamente all'area Intranet del computer. Questa impostazione consente al browser di inviare automaticamente le credenziali dell'utente attualmente connesso sotto forma di un ticket Kerberos per Azure AD. Il modo più semplice per aggiungere gli URL richiesti all'area Intranet consiste nel creare criteri di gruppo in Active Directory.

1.    Aprire gli strumenti di gestione dei criteri di gruppo.
2.    Modificare i criteri di gruppo applicati a tutti gli utenti, ad esempio il **Criterio dominio predefinito**.
3.    Passare a **Configurazione utente\Modelli amministrativi\Componenti di Windows\Internet Explorer\Pannello di controllo Internet\Scheda Sicurezza** e selezionare **Elenco di assegnazione siti ad aree**.
![Single Sign-On](./media/active-directory-aadconnect-sso/sso6.png)  
4.    Abilitare i criteri e immettere i valori/dati seguenti nella finestra di dialogo.  

        Valore: https://autologon.microsoftazuread-sso.com  
        Data 1  
        Valore: https://aadg.windows.net.nsatc.net  
        Data 1  
5.    Dovrebbe essere simile a quello riportato di seguito:  
![Single sign-on](./media/active-directory-aadconnect-sso/sso7.png)

6.    Fare clic su **OK**, quindi nuovamente su **OK**.

Gli utenti sono ora pronti per Single Sign-On.

>[!NOTE]
>Per impostazione predefinita, Chrome usa lo stesso insieme di URL di siti attendibili di Internet Explorer. Se per Chrome sono state configurate impostazioni diverse, sarà necessario aggiornare i siti separatamente.

## <a name="troubleshooting-single-sign-on-issues"></a>Risoluzione dei problemi di Single Sign-On
È importante accertarsi che il client sia configurato correttamente per Single Sign-On verificando che:

1.    Sia https://autologon.microsoftazuread-sso.com sia https://aadg.windows.net.nsatc.net vengano definiti all'interno della zona Intranet.
2.    La workstation sia stata aggiunta al dominio.
3.    L'utente sia connesso con un account di dominio.
4.    Il computer sia connesso alla rete aziendale.
5.    Verificare che l'ora del computer sia sincronizzata con quella di Active Directory e che l'ora del controller di dominio si discosti di un massimo di 5 minuti dall'ora corretta.
6.    Eliminare i ticket Kerberos esistenti nei client, ad esempio eseguendo il comando **klist purge** da un prompt dei comandi.

Dopo aver verificato i requisiti precedenti, è possibile esaminare i log della console del browser per informazioni aggiuntive. Questi log si trovano tra gli strumenti di sviluppo. Tali log contribuiscono a determinare il potenziale problema.

## <a name="event-log-entries"></a>Voci del log eventi
Se il controllo delle operazioni non riuscite è attivato, ogni volta che un utente accede con Single Sign-On viene registrata una voce nel log eventi del controller di dominio. Per individuare tali eventi, è possibile esaminare i log eventi per l'evento di sicurezza 4769 associato all'account computer **AzureADSSOAcc$**. Il filtro seguente trova tutti gli eventi di sicurezza associati all'account computer:

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```



<!--HONumber=Feb17_HO2-->


