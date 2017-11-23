---
title: Configurare HTTPS in un dominio personalizzato della rete per la distribuzione di contenuti di Azure | Microsoft Docs
description: Informazioni su come abilitare o disabilitare HTTPS nell'endpoint della rete CDN di Azure con un dominio personalizzato.
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: casoper
ms.openlocfilehash: 82de79cde208cdce1ed7cbd600f1e804ff1d45ff
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="configure-https-on-an-azure-content-delivery-network-custom-domain"></a>Configurare HTTPS in un dominio personalizzato della rete per la distribuzione di contenuti di Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft supporta il protocollo HTTPS per i domini personalizzati nella rete per la distribuzione di contenuti (CDN) di Azure. Il supporto di HTTPS per i domini personalizzati consente di distribuire contenuto protetto tramite SSL usando il nome del proprio dominio per una maggiore sicurezza dei dati in transito. Il flusso di lavoro per abilitare il protocollo HTTPS per il dominio personalizzato è più semplice grazie all'abilitazione con un unico clic e alla gestione completa dei certificati, il tutto senza costi aggiuntivi.

È fondamentale garantire la riservatezza e l'integrità dei dati sensibili delle applicazioni Web in transito. Il protocollo HTTPS garantisce la crittografia dei dati sensibili inviati su Internet. Offre attendibilità, autenticazione e protezione delle applicazioni Web da attacchi. Per impostazione predefinita, la rete CDN di Azure supporta il protocollo HTTPS per gli endpoint CDN. Se si crea un endpoint CDN dalla rete CDN di Azure, ad esempio `https://contoso.azureedge.net`, il protocollo HTTPS viene abilitato automaticamente. In più, il supporto di HTTPS per i domini personalizzati offre ora la possibilità di abilitare la distribuzione sicura anche per un dominio personalizzato, ad esempio `https://www.contoso.com`. 

Alcuni attributi chiave della funzionalità HTTPS sono:

- Assenza di costi aggiuntivi: non sono previsti costi per l'acquisizione o il rinnovo di certificati o per il traffico HTTPS. L'addebito è relativo solo ai GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. Il provisioning e il rinnovo dei certificati vengono eseguiti automaticamente prima della scadenza. Ciò elimina il rischio di interruzione del servizio a causa della scadenza di un certificato.

>[!NOTE] 
>Prima di abilitare il supporto di HTTPS, è necessario avere già definito un [dominio personalizzato della rete CDN di Azure](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Abilitazione di HTTPS

Per abilitare il protocollo HTTPS in un dominio personalizzato, seguire questa procedura:

### <a name="step-1-enable-the-feature"></a>Passaggio 1: Abilitare la funzionalità 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN Standard o Premium di Verizon.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per il quale si vuole abilitare la funzionalità HTTPS.

    ![Elenco dei domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Fare clic su **Sì** per abilitare HTTPS e quindi su **Applica**.

    ![Stato HTTPS del dominio personalizzato](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Passaggio 2: Convalidare il dominio

>[!IMPORTANT] 
>Prima che la funzionalità HTTPS sia attiva nel dominio personalizzato, è necessario completare la convalida del dominio. Il dominio deve essere approvato entro sei giorni lavorativi. Le richieste non approvate entro sei giorni lavorativi vengono annullate automaticamente. 

Dopo l'abilitazione di HTTPS nel dominio personalizzato, l'autorità di certificazione (CA) DigiCert convalida la proprietà del dominio contattandone il registrante in base alle informazioni sul registrante stesso in [WHOIS](http://whois.domaintools.com/). Per il contatto viene usato l'indirizzo di posta elettronica (impostazione predefinita) o il numero di telefono riportato nella registrazione WHOIS. 

>[!NOTE]
>Se si ha un record di autorizzazione dell'autorità di certificazione (CAA, Certificate Authority Authorization) con il provider DNS, il record deve includere DigiCert come CA valida. Un record CAA consente ai proprietari di domini di indicare ai propri provider DNS le CA autorizzate a emettere certificati per i loro domini. Se una CA riceve l'ordine di un certificato per un dominio dotato di record CAA ma la CA non è citata come autorità emittente autorizzata in tale record, non deve emettere il certificato per il dominio o il sottodominio. Per informazioni sulla gestione dei record CAA, vedere l'argomento relativo alla [gestione dei record CAA](https://support.dnsimple.com/articles/manage-caa-record/). Per informazioni su uno strumento per i record CAA, vedere [Strumento di supporto per record CAA](https://sslmate.com/caa/).

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

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice, ad esempio `contoso.com`. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Le richieste successive richiedono un'approvazione aggiuntiva.

Dopo l'approvazione, DigiCert aggiunge il nome del dominio personalizzato al certificato dei nomi alternativi del soggetto (SAN, Subject Alternative Name). Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

### <a name="step-3-wait-for-propagation"></a>Passaggio 3: Attendere la propagazione

Dopo la convalida del nome di dominio, per l'attivazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Abilitato** e i quattro passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

![Finestra di dialogo per l'abilitazione di HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per l'abilitazione di HTTPS. Dopo l'abilitazione di HTTPS, i quattro passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. All'attivazione di ogni passaggio, sotto il passaggio in corso vengono visualizzati dettagli aggiuntivi. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Passaggio dell'operazione | Dettagli del passaggio dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| | La richiesta HTTPS è in fase di invio. |
| | La richiesta HTTPS è stata inviata. |
| 2 Convalida del dominio | È stato inviato un messaggio di posta elettronica per chiedere la convalida della proprietà del dominio. La richiesta di convalida è in attesa di conferma. |
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

## <a name="disabling-https"></a>Disabilitazione di HTTPS

Dopo aver abilitato HTTPS in un dominio personalizzato, è possibile successivamente disabilitarlo. Per disabilitare HTTPS, seguire questa procedura.

### <a name="step-1-disable-the-feature"></a>Passaggio 1: Disabilitare la funzionalità 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN Standard o Premium di Verizon.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per cui si vuole disabilitare HTTPS.

    ![Elenco dei domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Fare clic su **No** per disabilitare HTTPS e quindi su **Applica**.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Passaggio 2: Attendere la propagazione

Per rendere effettiva la disabilitazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Disabilitato** e i tre passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

![Finestra di dialogo per la disabilitazione di HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

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

    Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e sicurezza di un certificato dedicato. Tutti i certificati SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata dei server.

5. *È possibile usare la funzionalità HTTPS del dominio personalizzato con la rete CDN di Azure fornita da Akamai?*

    Attualmente questa funzionalità è disponibile solo con la rete CDN di Azure fornita da Verizon. Microsoft si sta impegnando per fornire il supporto di questa funzionalità con la rete CDN di Azure fornita da Akamai nei prossimi mesi.

6. *È necessario avere un record di autorizzazione dell'autorità di certificazione presso il provider DNS?*
   No, attualmente un record di autorizzazione dell'autorità di certificazione non è obbligatorio. Se tuttavia se ne ha uno, deve includere DigiCert come CA valida.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare un [dominio personalizzato nell'endpoint della rete CDN di Azure](./cdn-map-content-to-custom-domain.md)


