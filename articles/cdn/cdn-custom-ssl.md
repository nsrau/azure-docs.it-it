---
title: Esercitazione - Configurare HTTPS in un dominio personalizzato della rete CDN di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come abilitare e disabilitare HTTPS nel dominio personalizzato dell'endpoint della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2018
ms.author: rli
ms.custom: mvc
ms.openlocfilehash: a8f2da5a68552c35a55a7bbb764afc7b36af6962
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Esercitazione: Configurare HTTPS in un dominio personalizzato della rete CDN di Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Questa esercitazione illustra come abilitare il protocollo HTTP per un dominio personalizzato associato a un endpoint della rete CDN (Content Delivery Network) di Azure. Usando il protocollo HTTPS nel dominio personalizzato (ad esempio, https:\//www.contoso.com), si assicura che i dati sensibili vengano recapitati in modo sicuro tramite crittografia SSL in caso di invio su Internet. HTTPS offre attendibilità, autenticazione e protezione delle applicazioni Web da attacchi. Il flusso di lavoro per abilitare il protocollo HTTPS è più semplice grazie all'abilitazione con un unico clic e alla gestione completa dei certificati, il tutto senza costi aggiuntivi.

Per impostazione predefinita, la rete CDN di Azure supporta il protocollo HTTPS in un nome host di endpoint della rete CDN. Se si crea un endpoint CDN, ad esempio https:\//contoso.azureedge.net, il protocollo HTTPS è abilitato per impostazione predefinita.  

Alcuni attributi chiave della funzionalità HTTPS sono:

- Assenza di costi aggiuntivi: non sono previsti costi per l'acquisizione o il rinnovo di certificati o per il traffico HTTPS. L'addebito è relativo solo ai GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. Il provisioning e il rinnovo dei certificati vengono eseguiti automaticamente prima della scadenza. Ciò elimina il rischio di interruzione del servizio a causa della scadenza di un certificato.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato
> - Convalidare il dominio
> - Disabilitare il protocollo HTTPS nel dominio personalizzato

## <a name="prerequisites"></a>prerequisiti

Prima di poter completare i passaggi di questa esercitazione, è necessario creare un profilo della rete CDN e almeno un endpoint della rete CDN. Per altre informazioni, vedere [Guida introduttiva: Creare un profilo e un endpoint della rete CDN di Azure](cdn-create-new-endpoint.md).

È inoltre necessario associare un dominio personalizzato della rete CDN di Azure nell'endpoint della rete CDN. Per altre informazioni, vedere [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md)

## <a name="enable-the-https-feature"></a>Abilitare la funzionalità HTTPS

Per abilitare il protocollo HTTPS in un dominio personalizzato, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della **rete CDN Standard di Azure con tecnologia Verizon** o della **rete CDN Premium di Azure con tecnologia Verizon**.

2. Nell'elenco di endpoint della rete CDN selezionare l'endpoint contenente il dominio personalizzato.

    ![Elenco di endpoint](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Viene visualizzata la pagina **Endpoint**.

3. Nell'elenco di domini personalizzati selezionare il dominio personalizzato per cui si vuole abilitare il protocollo HTTPS.

    ![Elenco dei domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Viene visualizzata la pagina **Dominio personalizzato**.

4. Selezionare **Sì** per abilitare il protocollo HTTPS, quindi selezionare **Applica**.

    ![Stato HTTPS del dominio personalizzato](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="validate-the-domain"></a>Convalidare il dominio

Se è già in uso un dominio personalizzato mappato all'endpoint personalizzato con un record CNAME record, passare a  
[Il dominio personalizzato è mappato all'endpoint della rete CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). In caso contrario, se la voce del record CNAME per l'endpoint non esiste più o se contiene il sottodominio cdnverify, passare a [Il dominio personalizzato non è mappato all'endpoint della rete CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Il dominio personalizzato è mappato all'endpoint della rete CDN da un record CNAME

Quando viene aggiunto un dominio personalizzato all'endpoint, nella tabella DNS del registrar viene creato un record CNAME per eseguire il mapping del nome host dell'endpoint della rete CDN. Se il record CNAME esiste già e non contiene il sottodominio cdnverify, l'autorità di certificazione (CA) DigiCert lo usa per convalidare la proprietà del dominio personalizzato. 

Il record CNAME deve avere il formato seguente, dove *Nome* è il nome del dominio personalizzato e *Valore* è il nome host dell'endpoint rete CDN:

| NOME            | type  | Valore                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Per altre informazioni sui record CNAME, vedere [Create the CNAME DNS record](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records) (Creare un record DNS CNAME).

Se il record CNAME è nel formato corretto, DigiCert verifica automaticamente il nome di dominio personalizzato e lo aggiunge al certificato SAN. DigitCert non invia alcun messaggio di verifica e non sarà necessario approvare la richiesta. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza. Passare ad [Attendere la propagazione](#wait-for-propagation). 

La convalida automatica richiede in genere qualche minuto. Se il dominio non viene convalidato entro un'ora, aprire un ticket di supporto.

>[!NOTE]
>Se si ha un record di autorizzazione dell'autorità di certificazione (CAA, Certificate Authority Authorization) con il provider DNS, il record deve includere DigiCert come CA valida. Un record CAA consente ai proprietari di domini di indicare ai propri provider DNS le CA autorizzate a emettere certificati per i loro domini. Se una CA riceve l'ordine di un certificato per un dominio dotato di record CAA ma la CA non è citata come autorità emittente autorizzata in tale record, non deve emettere il certificato per il dominio o il sottodominio. Per informazioni sulla gestione dei record CAA, vedere l'argomento relativo alla [gestione dei record CAA](https://support.dnsimple.com/articles/manage-caa-record/). Per informazioni su uno strumento per i record CAA, vedere [Strumento di supporto per record CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Il dominio personalizzato non è mappato all'endpoint della rete CDN

Se la voce di record CNAME per l'endpoint non esiste più o contiene il sottodominio cdnverify, seguire le istruzioni riportate in questo passaggio.

Dopo l'abilitazione di HTTPS nel dominio personalizzato, l'autorità di certificazione (CA) DigiCert convalida la proprietà del dominio contattandone il registrante in base alle informazioni sul registrante stesso in [WHOIS](http://whois.domaintools.com/). Per il contatto viene usato l'indirizzo di posta elettronica (impostazione predefinita) o il numero di telefono riportato nella registrazione WHOIS. Prima che la funzionalità HTTPS sia attiva nel dominio personalizzato, è necessario completare la convalida del dominio. Il dominio deve essere approvato entro sei giorni lavorativi. Le richieste non approvate entro sei giorni lavorativi vengono annullate automaticamente. 

![Record WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert invia un messaggio di verifica anche a indirizzi di posta elettronica aggiuntivi. Se le informazioni del registrante WHOIS sono private, verificare di poter eseguire l'approvazione direttamente da uno degli indirizzi seguenti:

admin@&lt;nome-dominio.com&gt;  
administrator@&lt;nome-dominio.com&gt;  
webmaster@&lt;nome-dominio.com&gt;  
hostmaster@&lt;nome-dominio.com&gt;  
postmaster@&lt;nome-dominio.com&gt;  

Entro pochi minuti si dovrebbe ricevere un messaggio di posta elettronica simile all'esempio seguente, in cui viene chiesto di approvare la richiesta. Se si usa un filtro per la posta indesiderata, aggiungere admin@digicert.com all'elenco degli indirizzi consentiti. Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.
    
![Messaggio di posta elettronica di convalida del dominio](./media/cdn-custom-ssl/domain-validation-email.png)

Facendo clic sul collegamento per l'approvazione, si verrà indirizzati al modulo di approvazione online seguente: 
    
![Modulo di convalida del dominio](./media/cdn-custom-ssl/domain-validation-form.png)

Seguire le istruzioni nel modulo. Sono disponibili due opzioni di verifica:

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice, ad esempio contoso.com. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Le richieste successive richiedono un'approvazione aggiuntiva.

Dopo l'approvazione, DigiCert aggiungerà il nome del dominio personalizzato al certificato SAN. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

## <a name="wait-for-propagation"></a>Attendere la propagazione

Dopo la convalida del nome di dominio, per l'attivazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Abilitato** e i quattro passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

![Finestra di dialogo per l'abilitazione di HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per l'abilitazione di HTTPS. Dopo l'abilitazione di HTTPS, i quattro passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. All'attivazione di ogni passaggio, sotto il passaggio in corso vengono visualizzati dettagli aggiuntivi sul passaggio secondario. Non tutti i passaggi secondari verranno eseguiti. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Passaggio dell'operazione | Dettagli del passaggio secondario dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| | La richiesta HTTPS è in fase di invio. |
| | La richiesta HTTPS è stata inviata. |
| 2 Convalida del dominio | Il dominio viene convalidato automaticamente se CNAME è mappato all'endpoint della rete CDN. In caso contrario, una richiesta di verifica verrà inviata all'indirizzo di posta elettronica elencato nel record di registrazione del dominio (registrante WHOIS). Verificare il dominio non appena possibile. |
| | La proprietà del dominio è stata convalidata. |
| | La richiesta di convalida della proprietà del dominio è scaduta (probabilmente perché il cliente non ha risposto entro 6 giorni). HTTPS non verrà abilitato nel dominio. * |
| | La richiesta di convalida della proprietà del dominio è stata rifiutata dal cliente. HTTPS non verrà abilitato nel dominio. * |
| 3 Provisioning del certificato | L'autorità di certificazione sta per rilasciare il certificato necessario per abilitare HTTPS nel dominio. |
| | Il certificato è stato rilasciato ed è in fase di distribuzione nella rete CDN. Questa operazione potrebbe richiedere fino a 6 ore. |
| | Il certificato è stato distribuito nella rete CDN. |
| 4 Operazione completata | HTTPS è stato abilitato nel dominio. |

\* Questo messaggio viene visualizzato solo se si è verificato un errore. 

Se si verifica un errore prima dell'invio della richiesta, viene visualizzato il messaggio di errore seguente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="clean-up-resources---disable-https"></a>Pulire le risorse - Disabilitare il protocollo HTTPS

Nei passaggi precedenti è stato abilitato il protocollo HTTPS nel dominio personalizzato. Se non si vuole più usare il dominio personalizzato con HTTPS, è possibile disabilitare il protocollo HTTPS seguendo questa procedura:

### <a name="disable-the-https-feature"></a>Disabilitare la funzionalità HTTPS 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della **rete CDN Standard di Azure con tecnologia Verizon** o della **rete CDN Premium di Azure con tecnologia Verizon**.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per cui si vuole disabilitare HTTPS.

    ![Elenco dei domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Fare clic su **No** per disabilitare HTTPS e quindi su **Applica**.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Attendere la propagazione

Per rendere effettiva la disabilitazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Disabilitato** e i tre passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

![Finestra di dialogo per la disabilitazione di HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per la disabilitazione di HTTPS. Al termine, i tre passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. All'attivazione di ogni passaggio, sotto di esso vengono visualizzati dettagli aggiuntivi. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Avanzamento dell'operazione | Dettagli dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| 2 Deprovisioning del certificato | Eliminazione del certificato |
| 3 Operazione completata | Il certificato è stato eliminato |

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Microsoft usa un certificato dei nomi alternativi soggetto (SAN) fornito da DigiCert. Un solo certificato SAN può proteggere più nomi di dominio completi.

2. *È possibile usare il certificato dedicato personale?*
    
    Non attualmente, ma questa possibilità è prevista nel piano d'azione.

3. *Cosa accade se non si riceve il messaggio di verifica del dominio da DigiCert?*

    Se si dispone di una voce CNAME per il dominio personalizzato che punta direttamente al nome host dell'endpoint (e non si usa il nome del sottodominio cdnverify), non viene visualizzato alcun messaggio di verifica del dominio. La convalida viene eseguita automaticamente. In caso contrario, se non è disponibile alcuna voce CNAME e non è stato ricevuto alcun messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e sicurezza di un certificato dedicato. Tutti i certificati SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata dei server.

5. *È possibile usare la funzionalità HTTPS del dominio personalizzato con la rete CDN di Azure fornita da Akamai?*

    Attualmente questa funzionalità è disponibile solo con la rete CDN di Azure fornita da Verizon. Microsoft si sta impegnando per fornire il supporto di questa funzionalità con la rete CDN di Azure fornita da Akamai nei prossimi mesi.

6. *È necessario avere un record di autorizzazione dell'autorità di certificazione presso il provider DNS?*

    No, attualmente un record di autorizzazione dell'autorità di certificazione non è obbligatorio. Se tuttavia se ne ha uno, deve includere DigiCert come CA valida.


## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> - Abilitazione del protocollo HTTPS nel dominio personalizzato
> - Convalida del dominio
> - Disabilitazione del protocollo HTTPS nel dominio personalizzato

Passare all'esercitazione successiva per informazioni su come configurare la memorizzazione nella cache nell'endpoint della rete CDN.

> [!div class="nextstepaction"]
> [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole di memorizzazione nella cache](cdn-caching-rules.md)

