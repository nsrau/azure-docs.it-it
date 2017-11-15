---
title: Protezione di asset della rete CDN di Azure con l'autenticazione basata su token | Microsoft Docs
description: Informazioni sull'uso dell'autenticazione basata su token per proteggere l'accesso agli asset della rete CDN di Azure.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/03/2017
ms.author: mezha
ms.openlocfilehash: 700f4c49bbcda1eccbcc7eafc703e625697fa2b4
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="securing-azure-content-delivery-network-assets-with-token-authentication"></a>Protezione di asset della rete per la distribuzione di contenuti (CDN) di Azure con l'autenticazione basata su token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Panoramica

L'autenticazione basata su token è un meccanismo che consente di impedire alla rete CDN di distribuire asset ai client non autorizzati, ad esempio per evitare il "collegamento attivo" del contenuto, in cui un altro sito Web, spesso una bacheca, usa gli asset senza autorizzazione. Il collegamento attivo può avere effetto sui costi di distribuzione di contenuti. Abilitando l'autenticazione del token nella rete CDN, le richieste vengono autenticate dai POP perimetrali della rete CDN prima che questa distribuisca i contenuti. 

## <a name="how-it-works"></a>Funzionamento

L'autenticazione basata su token verifica che le richieste vengano generate da un sito attendibile imponendo nelle richieste la presenza di un valore token contenente informazioni codificate sul richiedente. Il contenuto viene distribuito a un richiedente solo se le informazioni codificate soddisfano i requisiti. In caso contrario, le richieste vengono negate. È possibile impostare i requisiti usando uno o più dei parametri seguenti:

- Paese: consentire o negare le richieste che provengono dai paesi specificati dal relativo [codice paese](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: consentire solo le richieste che corrispondono all'asset o al percorso specificato.
- Host: consentire o negare le richieste che usano gli host specificati nell'intestazione della richiesta.
- Referrer: consentire o negare la richiesta dal referrer specificato.
- Indirizzo IP: consentire solo le richieste che hanno origine da un indirizzo IP o da una subnet IP specifica.
- Protocollo: consentire o negare le richieste basate sul protocollo usato per richiedere il contenuto.
- Scadenza: assegnare un periodo di data e ora per assicurare che un collegamento rimanga valido solo per un periodo di tempo limitato.

Per altre informazioni, vedere gli esempi di configurazione dettagliati per ogni parametro in [Configurazione dell'autenticazione basata su token](#setting-up-token-authentication).

Dopo aver generato un token crittografato, aggiungerlo come stringa di query alla fine del percorso dell'URL del file, ad esempio `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma di flusso di lavoro seguente descrive il modo in cui la rete CDN usa l'autenticazione basata su token per il funzionamento con l'app Web.

![Flusso di lavoro dell'autenticazione basata su token della rete CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logica di convalida dei token nell'endpoint di rete CDN
    
Il diagramma di flusso seguente illustra come la rete CDN di Azure convalida la richiesta client quando viene configurata l'autenticazione basata su token nell'endpoint di rete CDN.

![Logica di convalida dei token nella rete CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configurazione dell'autenticazione basata su token

1. Dal [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN e quindi fare clic su **Manage** (Gestisci) per avviare il portale supplementare.

    ![Pulsante Manage (Gestisci) del profilo di rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Passare il puntatore su **HTTP Large** (Large HTTP) e quindi fare clic su **Token Auth** (Autenticazione token) nel riquadro a comparsa. È poi possibile impostare i parametri di crittografia e la chiave di crittografia come indicato di seguito:

    1. Immettere una chiave di crittografia univoca nella casella **Primary Key** (Chiave primaria) e, facoltativamente, immettere una chiave di backup nella casella **Backup Key** (Chiave di backup).

        ![Chiave di configurazione per l'autenticazione basata su token di rete CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configurare i parametri di crittografia con lo strumento di crittografia. Grazie allo strumento è possibile consentire o negare le richieste in base a ora di scadenza, paese, referrer, protocollo e IP client (in qualsiasi combinazione). 

        ![Strumento di crittografia della rete CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

       Immettere i valori per uno o più parametri di crittografia seguenti nell'area **Encrypt Tool** (Strumento di crittografia):  

       - **ec-expire**: assegna un'ora di scadenza a un token. Le richieste inviate dopo l'ora di scadenza verranno negate. Questo parametro usa un timestamp Unix basato sul numero di secondi trascorsi dal periodo standard di `1/1/1970 00:00:00 GMT`. È possibile usare gli strumenti online per eseguire la conversione dall'ora solare all'ora Unix e viceversa. Ad esempio, se si vuole che il token scada in data `12/31/2016 12:00:00 GMT`, usare il valore del timestamp Unix `1483185600` come indicato di seguito. 
    
         ![Esempio ec_expire della rete CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
       - **ec-url-allow**: consente di adattare i token a un particolare asset o percorso. Limita l'accesso alle richieste il cui URL inizia con un percorso relativo specifico. Gli URL distinguono tra maiuscole e minuscole. Inserire più percorsi separandoli con una virgola. A seconda dei requisiti, è possibile configurare valori diversi per ogni livello di accesso. 
        
         Ad esempio, per l'URL `http://www.mydomain.com/pictures/city/strasbourg.png` sono consentite queste richieste per i valori di input seguenti:

         - Valore di input `/`: sono consentite tutte le richieste.
         - Valore di input `/pictures`: sono consentite le richieste riportate di seguito.
            - `http://www.mydomain.com/pictures.png`
            - `http://www.mydomain.com/pictures/city/strasbourg.png`
            - `http://www.mydomain.com/picturesnew/city/strasbourgh.png`
         - Valore di input `/pictures/`: sono consentite solo le richieste contenenti il percorso `/pictures/`, ad esempio `http://www.mydomain.com/pictures/city/strasbourg.png`.
         - Valore di input `/pictures/city/strasbourg.png`: sono consentite solo le richieste per questo specifico percorso e asset.
    
       - **ec-country-allow**: consente solo le richieste che hanno origine da uno o più paesi specificati. Le richieste che hanno origine da tutti gli altri paesi verranno negate. Usare codici paese e separarli con una virgola. Ad esempio, per consentire l'accesso solo da Stati Uniti e Francia, inserire US, FR nella casella, come illustrato di seguito.  
        
           ![Esempio ec_country_allow della rete CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

       - **ec-country-deny**: nega le richieste che hanno origine da uno o più paesi specificati. Le richieste che hanno origine da tutti gli altri paesi verranno consentite. Usare codici paese e separarli con una virgola. Ad esempio, per negare l'accesso da Stati Uniti e Francia, inserire US, FR nella casella.
    
       - **ec-ref-allow**: consente solo le richieste dal referrer specificato. Un referrer identifica l'URL della pagina Web collegata alla risorsa richiesta. Non includere nel protocollo il valore del parametro del referrer. Per il valore del parametro sono consentiti i tipi di input seguenti:
           - Un nome host o un nome host e un percorso.
           - Più referrer. Aggiungere più referrer separandoli con una virgola. Se si specifica un valore per il referrer, ma le informazioni sul referrer non vengono inviate nella richiesta a causa della configurazione del browser, queste richieste vengono negate per impostazione predefinita. 
           - Richieste con informazioni sui referrer mancanti. Per consentire questi tipi di richieste, immettere il testo "missing" o un valore vuoto. 
           - Sottodomini. Per consentire sottodomini, immettere un asterisco (\*). Ad esempio, per consentire tutti i sottodomini di `consoto.com`, immettere `*.consoto.com`. 
           
          L'esempio seguente mostra l'input per consentire l'accesso per le richieste da `www.consoto.com`, tutti i sottodomini in `consoto2.com` e le richieste con referrer vuoti o mancanti.
        
          ![Esempio ec_ref_allow della rete CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
       - **ec-ref-deny**: nega le richieste dal referrer specificato. L'implementazione è la stessa del parametro ec_ref_allow.
         
       - **ec-proto-allow**: consente solo le richieste dal protocollo specificato, ad esempio HTTP o HTTPS.
            
       - **ec-proto-deny**: nega le richieste dal protocollo specificato, ad esempio HTTP o HTTPS.
    
       - **ec-clientip**: limita l'accesso all'indirizzo IP del richiedente specificato. Sono supportati sia IPV4 che IPV6. È possibile specificare un solo indirizzo IP o una sola subnet IP per la richiesta.
            
         ![Esempio ec_clientip della rete CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)

    3. Dopo aver immesso i valori dei parametri di crittografia, selezionare il tipo di chiave da crittografare (se sono state create una chiave primaria e una chiave di backup) dall'elenco **Key To Encrypt** (Chiave da crittografare), una versione di crittografia dall'elenco **Encryption Version** (Versione di crittografia) e fare clic su **Encrypt** (Crittografa).
        
    4. È possibile testare il token con lo strumento di decrittografia. Incollare il valore del token nella casella **Token to Decrypt** (Token da decrittografare). Selezionare il tipo di chiave di crittografia da decrittografare dall'elenco a discesa **Key to Decrypt** (Chiave da decrittografare) e fare clic su **Decrypt** (Decrittografa).

    5. Facoltativamente, è possibile personalizzare il tipo di codice di risposta restituito quando viene negata una richiesta. Selezionare il codice dall'elenco a discesa **Response Code** (Codice di risposta) e fare clic su **Save** (Salva). Il codice di risposta **403** (Forbidden (Non consentito)) è selezionato per impostazione predefinita. Per alcuni codici di risposta è anche possibile immettere l'URL della pagina di errore nella casella **Header Value** (Valore dell'intestazione). 

    6. Dopo aver generato un token crittografato, aggiungerlo come stringa di query alla fine del file nel percorso dell'URL, ad esempio `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.

3. In **HTTP Large** (HTTP grande) fare clic su **Rules Engine** (Motore regole di business). Il motore di regole di business consente di definire i percorsi per applicare la funzionalità, abilitare la funzionalità di autenticazione basata su token e altre funzionalità correlate all'autenticazione basata su token. Per altre informazioni, vedere [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md).

    1. Selezionare una regola esistente o crearne una nuova per definire l'asset o il percorso per il quale applicare l'autenticazione basata su token. 
    2. Per abilitare l'autenticazione basata su token in una regola, selezionare  **[Token Auth](cdn-rules-engine-reference-features.md#token-auth)** (Autenticazione basata su token) dall'elenco a discesa **Features** (Funzionalità) e fare clic su **Enabled (Abilitato)**. Fare clic su **Update** (Aggiorna) se si aggiorna una regola o su **Add** (Aggiungi) se si crea una regola.
        
    ![Esempio di abilitazione dell'autenticazione basata su token nel motore di regole di business della rete CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Nel motore di regole di business è anche possibile abilitare altre funzionalità correlate all'autenticazione basata su token. Per abilitare le funzionalità seguenti, selezionarle dall'elenco a discesa **Features** (Funzionalità) e fare clic su **Enabled** (Abilitato).
    
    - **[Token Auth Denial Code](cdn-rules-engine-reference-features.md#token-auth-denial-code)** (Codice negazione autenticazione token): determina il tipo di risposta restituita all'utente quando viene negata una richiesta. Le regole impostate qui sostituiscono il codice di risposta impostato nella sezione **Custom Denial Handling** (Gestione negazione personalizzata) della pagina di autenticazione basata su token.
    - **[Token Auth Ignore URL Case (Maiuscole/minuscole URL rifiuto autenticazione token)](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: determina se l'URL usato per convalidare il token applica la distinzione tra maiuscole e minuscole.
    - **[Token Auth Parameter (Parametro autenticazione token)](cdn-rules-engine-reference-features.md#token-auth-parameter)**: rinomina il parametro della stringa di query dell'autenticazione basata su token visualizzato nell'URL richiesto. 
        
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

Per informazioni, vedere [Panoramica della rete CDN](cdn-overview.md).
