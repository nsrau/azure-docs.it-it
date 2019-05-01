---
title: Protezione di asset della rete CDN di Azure con l'autenticazione basata su token | Documentazione Microsoft
description: Informazioni sull'uso dell'autenticazione basata su token per proteggere l'accesso agli asset della rete CDN di Azure.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: 640c65b1f6995a6c5fb7a3a1fcfeb580aecf5c43
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869416"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Protezione di asset della rete CDN di Azure con l'autenticazione basata su token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica

L'autenticazione basata su token è un meccanismo che consente di impedire alla rete CDN di distribuire asset ai client non autorizzati, L'autenticazione basata su token viene eseguita generalmente per evitare il *collegamento attivo* del contenuto, in cui un altro sito Web, come ad esempio una bacheca, usa gli asset senza autorizzazione. Il collegamento attivo può avere effetto sui costi di distribuzione di contenuti. Abilitando l'autenticazione del token nella rete CDN, le richieste vengono autenticate dal server perimetrale della rete CDN prima che questa distribuisca i contenuti. 

## <a name="how-it-works"></a>Funzionamento

L'autenticazione basata su token verifica che le richieste vengano generate da un sito attendibile imponendo nelle richieste la presenza di un valore token contenente informazioni codificate sul richiedente. Il contenuto viene distribuito a un richiedente solo se le informazioni codificate soddisfano i requisiti. In caso contrario, le richieste vengono negate. È possibile impostare i requisiti usando uno o più dei parametri seguenti:

- Paese: Consentire o negare le richieste che provengono da paesi/aree geografiche specificati dal loro [internazionale](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Consentire solo le richieste che corrispondono all'asset specificato o un percorso.
- Host: Consentire o negare le richieste che usano gli host specificati nell'intestazione della richiesta.
- Referrer: Consentire o negare la richiesta dal referrer specificato.
- Indirizzo IP: Consentire solo le richieste provenienti dal specifico indirizzo IP o subnet IP.
- Protocollo: Consentire o negare le richieste basate sul protocollo usato per richiedere il contenuto.
- Ora di scadenza: Assegnare un periodo di data e ora per assicurare che un collegamento rimanga valido solo per un periodo di tempo limitato.

Per altre informazioni, vedere gli esempi di configurazione dettagliati per ogni parametro in [Configurazione dell'autenticazione basata su token](#setting-up-token-authentication).

>[!IMPORTANT] 
> Se per un qualsiasi percorso in questo account è abilitata l'autorizzazione basata su token, è possibile usare solo la modalità standard-cache per la memorizzazione nella cache delle stringhe di query. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma di flusso di lavoro seguente descrive il modo in cui la rete CDN usa l'autenticazione basata su token per il funzionamento con l'app Web.

![Flusso di lavoro dell'autenticazione basata su token della rete CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logica di convalida dei token nell'endpoint di rete CDN
    
Il diagramma di flusso seguente illustra come la rete CDN di Azure convalida la richiesta di un client quando viene configurata l'autenticazione basata su token nell'endpoint di rete CDN.

![Logica di convalida dei token nella rete CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configurazione dell'autenticazione basata su token

1. Dal [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN e quindi selezionare **Manage** (Gestisci) per avviare il portale supplementare.

    ![Pulsante Manage (Gestisci) del profilo di rete CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Passare il puntatore su **HTTP Large** (Large HTTP) e quindi selezionare **Token Auth** (Autenticazione token) nel riquadro a comparsa. È poi possibile impostare i parametri di crittografia e la chiave di crittografia come indicato di seguito:

   1. Creare uno o più chiavi di crittografia. Una chiave di crittografia fa distinzione tra maiuscole e minuscole e può contenere qualsiasi combinazione di caratteri alfanumerici. Eventuali altri tipi di caratteri, inclusi gli spazi, non sono consentiti. La lunghezza massima consentita è di 250 caratteri. Per garantire che le chiavi di crittografia vengano generate in modo casuale, è consigliabile crearle usando lo [strumento OpenSSL](https://www.openssl.org/). 

      Lo strumento OpenSSL presenta la sintassi seguente:

      ```rand -hex <key length>```

      Ad esempio: 

      ```OpenSSL> rand -hex 32``` 

      Per evitare tempi di inattività, creare una chiave primaria e una di backup. Quando la chiave primaria viene aggiornata, una chiave di backup garantisce l'accesso continuo ai contenuti.
    
   2. Immettere una chiave di crittografia univoca nella casella **Primary Key** (Chiave primaria) e, facoltativamente, immettere una chiave di backup nella casella **Backup Key** (Chiave di backup).

   3. Selezionare la versione minima di crittografia per ogni chiave nel relativo elenco **Minimum Encryption Version** (Versione minima di crittografia) e quindi selezionare **Update** (Aggiorna):
      - **V2**: Indica che la chiave può essere usata per generare i token di versione 2.0 e 3.0. Usare questa opzione solo se si esegue una transizione da una chiave di crittografia legacy versione 2.0 a una chiave di versione 3.0.
      - **V3**: (Scelta consigliata) Indica che la chiave può essere usata solo per generare token di versione 3.0.

      ![Chiave di configurazione per l'autenticazione basata su token di rete CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Per configurare i parametri di crittografia e generare un token, usare lo strumento di crittografia. Con lo strumento di crittografia, è possibile consentire o negare le richieste basate su ora di scadenza, paese, referrer, protocollo e indirizzo IP del client (con qualsiasi combinazione). Sebbene non esista alcun limite al numero e alla combinazione di parametri che possono essere combinati per formare un token, la lunghezza totale di un token è limitata a 512 caratteri. 

      ![Strumento di crittografia della rete CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Immettere i valori per uno o più parametri di crittografia seguenti nella sezione **Encrypt Tool** (Strumento di crittografia): 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nome parametro</th> 
      >   <th>DESCRIZIONE</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Assegna un'ora di scadenza a un token, dopo la quale il token scade. Le richieste inviate dopo l'ora di scadenza verranno negate. Questo parametro usa un timestamp Unix basato sul numero di secondi trascorsi dal periodo standard Unix di `1/1/1970 00:00:00 GMT`. È possibile usare gli strumenti online per eseguire la conversione dall'ora solare all'ora Unix e viceversa.> 
      >    Ad esempio, se si vuole che il token scada in data `12/31/2016 12:00:00 GMT`, inserire il valore del timestamp Unix `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Consente di adattare i token a un particolare asset o percorso. Limita l'accesso alle richieste il cui URL inizia con un percorso relativo specifico. Gli URL distinguono tra maiuscole e minuscole. Inserire più percorsi separandoli con una virgola; non aggiungere spazi. A seconda dei requisiti, è possibile configurare valori diversi per ogni livello di accesso.> 
      >    Ad esempio, per l'URL `http://www.mydomain.com/pictures/city/strasbourg.png` sono consentite queste richieste per i valori di input seguenti: 
      >    <ul>
      >       <li>Valore di input `/`: Sono consentite tutte le richieste.</li>
      >       <li>Valore di input `/pictures`: sono consentite le richieste riportate di seguito. <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Valore di input `/pictures/`: Solo le richieste contenenti il `/pictures/` percorso sono consentiti. Ad esempio: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Valore di input `/pictures/city/strasbourg.png`: Sono consentite solo le richieste per questo specifico percorso e asset.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Consente solo le richieste che hanno origine da uno o più paesi specificati. Vengono negate le richieste originate da tutti gli altri paesi/aree geografiche. Uso di un [codice paese ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) di due lettere per ogni singolo paese e separarli tra loro con una virgola; non aggiungere uno spazio. Ad esempio, se si desidera consentire l'accesso solo da Stati Uniti e Francia, inserire `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nega le richieste che hanno origine da uno o più paesi specificati. Sono consentite richieste che hanno origine da tutti gli altri paesi/aree geografiche. L'implementazione è la stessa del parametro <b>ec_country_allow</b>. Se il codice di un paese è presente in entrambi i parametri <b>ec_country_allow</b> e <b>ec_country_deny</b>, il parametro <b>ec_country_allow</b> ha la precedenza.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Consente solo le richieste dal referrer specificato. Un referrer identifica l'URL della pagina Web collegata alla risorsa richiesta. Non includere il protocollo nel valore del parametro.> 
      >    Sono consentiti i tipi di input seguenti:
      >    <ul>
      >       <li>Un nome host o un nome host e un percorso.</li>
      >       <li>Più referrer. Aggiungere più referrer separandoli con una virgola; non aggiungere uno spazio. Se si specifica un valore per il referrer, ma le informazioni sul referrer non vengono inviate nella richiesta a causa della configurazione del browser, la richiesta viene negata per impostazione predefinita.</li> 
      >       <li>Richieste con informazioni sui referrer mancanti o vuote. Per impostazione predefinita, il parametro <b>ec_ref_allow</b> blocca questi tipi di richieste. Per consentire queste richieste, immettere il testo "missing" o inserire un valore vuoto (usando una virgola finale).</li> 
      >       <li>Sottodomini. Per consentire sottodomini, immettere un asterisco (\*). Ad esempio, per consentire tutti i sottodomini di `contoso.com`, immettere `*.contoso.com`.</li>
      >    </ul> 
      >    Ad esempio, per consentire l'accesso per le richieste da `www.contoso.com`, tutti i sottodomini in `contoso2.com` e le richieste con referrer vuoti o mancanti, inserire `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Respinge le richieste dal referrer specificato. L'implementazione è la stessa del parametro <b>ec_ref_allow</b>. Se un referrer è presente in entrambi i parametri <b>ec_ref_allow</b> e <b>ec_ref_deny</b>, il parametro <b>ec_ref_allow</b> ha la precedenza.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Consente solo le richieste dal protocollo specificato. I valori validi sono `http`, `https` o `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Respinge le richieste dal protocollo specificato. L'implementazione è la stessa del parametro <b>ec_proto_allow</b>. Se un protocollo è presente in entrambi i parametri <b>ec_proto_allow</b> e <b>ec_proto_deny</b>, il parametro <b>ec_proto_allow</b> ha la precedenza.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Limita l'accesso all'indirizzo IP del richiedente specificato. Sono supportati sia IPV4 che IPV6. È possibile specificare un singolo indirizzo IP di richiesta o gli indirizzi IP associati a una subnet specifica. Ad esempio, `11.22.33.0/22` consente le richieste provenienti da indirizzi IP che vanno sa 11.22.32.1 a 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Dopo aver immesso i valori dei parametri di crittografia, selezionare una chiave da crittografare (se sono state create una chiave primaria e una chiave di backup) dall'elenco **Key To Encrypt** (Chiave da crittografare).
    
   6. Selezionare una versione di crittografia dal **versione di crittografia** elenco: **V2** per la versione 2 o **V3** della versione 3 (scelta consigliata). 

   7. Selezionare **Encrypt** (Crittografa) per generare il token.

      Il token generato viene visualizzato nella casella **Generated Token** (Token generato). Per usare il token, aggiungerlo come stringa di query alla fine del file nel percorso dell'URL, Ad esempio: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Facoltativamente, verificare il token con lo strumento per decrittografare in modo che sia possibile visualizzare i parametri del token. Incollare il valore del token nella casella **Token to Decrypt** (Token da decrittografare). Selezionare la chiave di crittografia da usare dall'elenco **Key to Decrypt** (Chiave da decrittografare) e quindi selezionare **Decrypt** (Decrittografa).

      I parametri del token decrittografato vengono visualizzati nella casella **Original Parameters** (Parametri originali).

   9. Facoltativamente, è possibile personalizzare il tipo di codice di risposta restituito quando viene negata una richiesta. Selezionare **Abilitato**, quindi selezionare il codice di risposta dall'elenco **Codice di risposta**. Il **Nome intestazione** viene impostato automaticamente su **Posizione**. Selezionare **Save** (Salva) per implementare il nuovo codice di risposta. Per alcuni codici di risposta è anche necessario immettere l'URL della pagina di errore nella casella **Valore intestazione**. Il codice di risposta **403** (Forbidden (Non consentito)) è selezionato per impostazione predefinita. 

3. In **HTTP Large** (HTTP grande) selezionare **Rules Engine** (Motore regole di business). Il motore di regole di business consente di definire i percorsi per applicare la funzionalità, abilitare la funzionalità di autenticazione basata su token e altre funzionalità correlate all'autenticazione basata su token. Per altre informazioni, vedere [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md).

   1. Selezionare una regola esistente o crearne una nuova per definire l'asset o il percorso per il quale applicare l'autenticazione basata su token. 
   2. Per abilitare l'autenticazione basata su token in una regola, selezionare **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)** (Autenticazione basata su token) dall'elenco **Features** (Funzionalità) e fare clic su **Abilitato**. Selezionare **Update** (Aggiorna) se si aggiorna una regola o su **Add** (Aggiungi) se si crea una regola.
        
      ![Esempio di abilitazione dell'autenticazione basata su token nel motore di regole di business della rete CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Nel motore di regole di business è anche possibile abilitare altre funzionalità correlate all'autenticazione basata su token. Per abilitare le funzionalità seguenti, selezionarle dall'elenco **Features** (Funzionalità) e fare clic su **Abilitato**.
    
   - **[Token Auth Denial Code](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Determina il tipo di risposta che viene restituito a un utente quando una richiesta viene rifiutata. Le regole impostate qui sostituiscono il codice di risposta impostato nella sezione **Custom Denial Handling** (Gestione negazione personalizzata) della pagina di autenticazione basata su token.

   - **[Token Auth Ignore URL Case](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Determina se l'URL usato per convalidare il token è tra maiuscole e minuscole.

   - **[Parametro autenticazione token](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Rinomina il parametro di stringa di query di autenticazione basata su token visualizzato nell'URL richiesto. 
        
     ![Esempio di impostazioni di autenticazione basata su token nel motore di regole di business della rete CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Per personalizzare il token, accedere al codice sorgente in [GitHub](https://github.com/VerizonDigital/ectoken).
   I linguaggi disponibili includono:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Prezzi dei provider e funzionalità della rete CDN di Azure

Per informazioni sulle funzionalità, vedere [le funzionalità del prodotto della rete CDN di Azure](cdn-features.md). Per informazioni sui prezzi, vedere [Prezzi della rete per la distribuzione di contenuti](https://azure.microsoft.com/pricing/details/cdn/).
