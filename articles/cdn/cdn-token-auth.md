---
title: Protezione di asset della rete CDN di Azure con l'autenticazione basata su token | Documentazione Microsoft
description: Uso dell'autenticazione basata su token per proteggere l'accesso agli asset della rete CDN di Azure.
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
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Protezione di asset della rete CDN di Azure con l'autenticazione basata su token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Panoramica

L'autenticazione basata su token è un meccanismo che consente di impedire alla rete CDN di distribuire asset ai client non autorizzati,  ad esempio per evitare il "collegamento attivo" del contenuto, in cui un altro sito Web, spesso una bacheca, usa gli asset senza autorizzazione.  Questo può avere effetto sui costi di distribuzione di contenuti. Abilitando questa funzionalità nella rete CDN, le richieste verranno autenticate dai POP perimetrali della rete CDN prima di distribuire il contenuto. 

## <a name="how-it-works"></a>Funzionamento

L'autenticazione basata su token verifica che le richieste vengano generate da un sito attendibile imponendo nelle richieste la presenza di un valore token contenente informazioni codificate sul richiedente. Il contenuto viene distribuito al richiedente solo se le informazioni codificate soddisfano i requisiti. In caso contrario, le richieste verranno negate. È possibile configurare il requisito usando uno o più dei parametri seguenti.

- Paese: consente o nega le richieste che hanno origine dai paesi specificati.  [Elenco di codici paesi validi.](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: consente solo all'asset o al percorso specificato di effettuare la richiesta.  
- Host: consente o nega le richieste usando gli host specificati nell'intestazione della richiesta.
- Referrer: consente o nega al referrer specificato di effettuare la richiesta.
- Indirizzo IP: consente solo le richieste che hanno origine da un indirizzo IP o da una subnet IP specifica.
- Protocollo: consente o blocca le richieste basate sul protocollo usato per richiedere il contenuto.
- Scadenza: assegna un periodo di data e ora per assicurare che un collegamento rimanga valido solo per un periodo di tempo limitato.

Vedere l'esempio di configurazione dettagliato per ogni parametro.

## <a name="reference-architecture"></a>Architettura di riferimento

Vedere di seguito un'architettura di riferimento della configurazione dell'autenticazione basata su token nella rete CDN per usare l'app Web.

![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Logica di convalida dei token nell'endpoint di rete CDN
    
Questo grafico illustra come la rete CDN di Azure convalida la richiesta client quando viene configurata l'autenticazione basata su token nell'endpoint di rete CDN.

![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configurazione dell'autenticazione basata su token

1. Dal [portale di Azure](https://portal.azure.com) passare al profilo di rete CDN e quindi fare clic sul pulsante **Gestisci**  per avviare il portale supplementare.

    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-rules-engine/cdn-manage-btn.png)

2. Passare il puntatore su **HTTP Large** (Large HTTP) e quindi fare clic su **Token Auth** (Autenticazione token) nel riquadro a comparsa. In questa scheda si configureranno la chiave di crittografia e i parametri di crittografia.

    1. Immettere una chiave di crittografia univoca per **Chiave primaria**.  Immetterne un'altra per **Backup Key** (Chiave di backup).

        ![Chiave di configurazione per l'autenticazione basata su token di rete CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. Configurare i parametri di crittografia con lo strumento di crittografia. Consentire o negare le richieste in base a ora di scadenza, paese, referrer, protocollo e IP client. È possibile usare qualsiasi combinazione.

        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire: assegna un'ora di scadenza di un token dopo un periodo di tempo specificato. Le richieste inviate dopo l'ora di scadenza verranno negate. Questo parametro usa il timestamp Unix, basato sui secondi dal periodo standard 1/1/1970 00:00:00 GMT. È possibile usare gli strumenti online per eseguire la conversione dall'ora solare all'ora Unix.  Ad esempio, per configurare la scadenza del token al 31/12/2016 12:00:00 GMT, usare l'ora Unix 1483185600 come illustrato sotto:
    
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow: consente di adattare i token a un particolare asset o percorso. Limita l'accesso alle richieste il cui URL inizia con un percorso relativo specifico. È possibile inserire più percorsi separandoli con una virgola. Gli URL distinguono tra maiuscole e minuscole. A seconda del requisito, è possibile configurare un valore diverso per ogni livello di accesso. Di seguito sono elencati alcuni scenari:
        
            Se l'URL è: http://www.mydomain.com/pictures/city/strasbourg.png. Vedere il valore di input "" e di conseguenza il livello di accesso

            1. Valore di input "/": tutte le richieste verranno consentite
            2. Valore di input "/pictures": verranno consentite tutte le richieste seguenti
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. Valore di input "/pictures/": verranno consentite solo le richieste per /pictures/
            4. Valore di input "/pictures/city/strasbourg.png": consente solo le richieste per questo asset
    
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow: consente solo le richieste che hanno origine da uno o più paesi specificati. Le richieste che hanno origine da tutti gli altri paesi verranno negate. Usare il codice paese per configurare i parametri e separare ogni codice paese con una virgola. Ad esempio, per consentire l'accesso da Stati Uniti e Francia, inserire US, FR nella colonna, come illustrato sotto.  
        
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny: nega le richieste che hanno origine da uno o più paesi specificati. Le richieste che hanno origine da tutti gli altri paesi verranno consentite. Usare il codice paese per configurare i parametri e separare ogni codice paese con una virgola. Ad esempio, per negare l'accesso da Stati Uniti e Francia, inserire US, FR nella colonna.
    
        - ec-ref-allow: consente solo le richieste dal referrer specificato. Un referrer identifica l'URL della pagina Web collegata alla risorsa richiesta. Il valore del parametro del referrer non deve includere il protocollo. È possibile inserire un nome host e/o un determinato percorso in tale nome host. È anche possibile aggiungere più referrer in un singolo parametro separandoli con una virgola. Se si è specificato un valore per il referrer, ma le informazioni sul referrer non vengono inviate nella richiesta a causa della configurazione del browser, queste richieste verranno negate per impostazione predefinita. È possibile assegnare "Missing" o un valore vuoto nel parametro per consentire queste richieste con informazioni sul referrer mancanti. È anche possibile usare "*.consoto.com" per consentire tutti i sottodomini di consoto.com.  Ad esempio, per consentire l'accesso per le richieste da www.consoto.com, tutti i sottodomini in consoto2.com e le richieste con refferer vuoti o mancanti, inserire il valore seguente:
        
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny: nega le richieste dal referrer specificato. Vedere i dettagli e l'esempio del parametro "ec-ref-allow".
         
        - ec-proto-allow: consente solo le richieste dal protocollo specificato, ad esempio http o https.
        
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny: nega le richieste dal protocollo specificato, ad esempio http o https.
    
        - ec-clientip: limita l'accesso all'indirizzo IP del richiedente specificato. Sono supportati sia IPV4 che IPV6. È possibile specificare un solo indirizzo IP o una sola subnet IP per la richiesta.
            
        ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. È possibile testare il token con lo strumento di decrittografia.

    4. È anche possibile personalizzare il tipo di risposta che verrà restituita all'utente quando la richiesta viene negata. Per impostazione predefinita, si usa 403.

3. Ora fare clic sulla scheda **Motore regole di business** in **HTTP Large** (Large HTTP). In questa scheda è possibile definire i percorsi a cui applicare la funzionalità, abilitare la funzionalità di autenticazione basata su token e abilitare altre funzionalità correlate all'autenticazione basata su token.

    - Usare la colonna "IF" per definire l'asset o il percorso a cui si vuole applicare l'autenticazione basata su token. 
    - Fare clic su "Token Auth" (Autenticazione token) nell'elenco a discesa della funzionalità per abilitare l'autenticazione basata su token.
        
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. Nella scheda **Motore regole di business** è possibile abilitare alcune funzionalità aggiuntive.
    
    - Token auth denial code (Codice negazione autenticazione token): determina il tipo di risposta che verrà restituita all'utente quando una richiesta viene negata. Le regole configurate qui eseguiranno l'override dell'impostazione del codice di negazione nella scheda dell'autorizzazione basata su token.
    - Token auth ignore (Rifiuto autenticazione token): determina se l'URL usato per convalidare il token applicherà la distinzione tra maiuscole e minuscole.
    - Token auth parameter (Parametro autenticazione token): rinomina il parametro della stringa di query dell'autenticazione basata su token visualizzato nell'URL richiesto. 
        
    ![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. È possibile personalizzare il token che è un'applicazione che genera un token per le funzionalità di autenticazione basata su token. Il codice sorgente è accessibile qui in [GitHub](https://github.com/VerizonDigital/ectoken).
I linguaggi disponibili includono:
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Prezzi dei provider e funzionalità della rete CDN di Azure

Vedere l'argomento [Panoramica della rete CDN](cdn-overview.md).
