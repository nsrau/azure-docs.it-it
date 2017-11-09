---
title: Abilitare o disabilitare HTTPS in un dominio personalizzato della rete per la distribuzione di contenuti di Azure | Microsoft Docs
description: Informazioni su come abilitare o disabilitare HTTPS nell'endpoint della rete CDN di Azure con un dominio personalizzato.
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>Abilitare o disabilitare HTTPS in un dominio personalizzato della rete per la distribuzione di contenuti di Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Il supporto di HTTPS per i domini personalizzati della rete per la distribuzione di contenuti (CDN) di Microsoft Azure consente di distribuire contenuto sicuro tramite SSL usando il nome del proprio dominio per una maggiore sicurezza dei dati in transito. Il flusso di lavoro end-to-end per abilitare il protocollo HTTPS per il dominio personalizzato è più semplice grazie all'abilitazione con un unico clic e alla gestione completa dei certificati, il tutto senza costi aggiuntivi.

È fondamentale garantire la riservatezza e l'integrità di tutti i dati sensibili delle applicazioni Web in transito. Il protocollo HTTPS garantisce la crittografia dei dati sensibili inviati su Internet. Offre attendibilità, autenticazione e protezione delle applicazioni Web da attacchi. La rete CDN di Azure supporta attualmente il protocollo HTTPS su un endpoint della rete CDN. Se si crea un endpoint CDN dalla rete CDN di Azure, ad esempio https://contoso.azureedge.net, il protocollo HTTPS è abilitato per impostazione predefinita. La funzionalità HTTPS per i domini personalizzati offre ora la possibilità di abilitare la distribuzione sicura anche per un dominio personalizzato, ad esempio https://www.contoso.com. 

Alcuni attributi chiave della funzionalità HTTPS sono:

- Assenza di costi aggiuntivi: non sono previsti costi per l'acquisizione o il rinnovo di certificati o per il traffico HTTPS. L'addebito è relativo solo ai GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. Il provisioning e il rinnovo dei certificati vengono eseguiti automaticamente prima della scadenza. In questo modo si riduce completamente il rischio di interruzione del servizio a causa della scadenza di un certificato.

>[!NOTE] 
>Prima di abilitare il supporto di HTTPS, è necessario avere già definito un [dominio personalizzato della rete CDN di Azure](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Abilitazione di HTTPS

Per abilitare HTTPS, seguire questa procedura.

### <a name="step-1-enable-the-feature"></a>Passaggio 1: Abilitare la funzionalità 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN Standard o Premium di Verizon.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per il quale si vuole abilitare la funzionalità HTTPS.

    ![Pannello Endpoint](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Fare clic su **Sì** per abilitare HTTPS e quindi su **Applica**.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Passaggio 2: Convalidare il dominio

>[!IMPORTANT] 
>Prima che la funzionalità HTTPS sia attiva nel dominio personalizzato, è necessario completare la convalida del dominio. Il dominio deve essere approvato entro sei giorni lavorativi. Le richieste non approvate entro sei giorni lavorativi verranno automaticamente annullate. 

Dopo l'abilitazione di HTTPS nel dominio personalizzato, il provider di certificati HTTPS DigiCert convalida la proprietà del dominio contattandone il registrante in base alle informazioni del registrante [WHOIS](http://whois.domaintools.com/) del dominio. Per il contatto viene usato l'indirizzo di posta elettronica (impostazione predefinita) o il numero di telefono riportato nella registrazione WHOIS. 

![Record WHOIS](./media/cdn-custom-ssl/whois-record.png)

DigiCert invierà il messaggio di posta elettronica di verifica anche agli indirizzi seguenti. Se le informazioni del registrante WHOIS sono private, verificare di poter eseguire l'approvazione direttamente da uno di questi indirizzi:

admin@&lt;nome-dominio.com&gt;  
administrator@&lt;nome-dominio.com&gt;  
webmaster@&lt;nome-dominio.com&gt;  
hostmaster@&lt;nome-dominio.com&gt;  
postmaster@&lt;nome-dominio.com&gt;  

Entro pochi minuti si dovrebbe ricevere un messaggio di posta elettronica simile all'esempio seguente, in cui viene chiesto di approvare la richiesta. Se si usa un filtro per la posta indesiderata, aggiungere admin@digicert.com all'elenco degli indirizzi consentiti. Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.
    
![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/domain-validation-email.png)

Facendo clic sul collegamento per l'approvazione, si verrà indirizzati al modulo di approvazione online seguente: 
    
![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/domain-validation-form.png)

Seguire le istruzioni nel modulo. Sono disponibili due opzioni di verifica:

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice, ad esempio contoso.com. Questo approccio è consigliato se si prevede di aggiungere in futuro altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Le richieste successive richiederanno un'approvazione aggiuntiva.

Dopo l'approvazione, DigiCert aggiungerà il nome del dominio personalizzato al certificato dei nomi alternativi soggetto. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

### <a name="step-3-wait-for-propagation"></a>Passaggio 3: Attendere la propagazione

Dopo la convalida del nome di dominio, per l'attivazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato di "HTTPS personalizzato" nel portale di Azure è impostato su "Abilitato" e i quattro passaggi dell'operazione nel pannello HTTPS dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

![Finestra di dialogo per l'abilitazione di HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per l'abilitazione di HTTPS. Al termine, i quattro passaggi dell'operazione vengono visualizzati nel pannello HTTPS dominio personalizzato. All'attivazione di ogni passaggio, sotto il passaggio in corso vengono visualizzati dettagli aggiuntivi. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

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

\* Questo messaggio verrà visualizzato solo se si è verificato un errore. 

Se si verifica un errore prima dell'invio della richiesta, verrà visualizzato il messaggio di errore seguente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Disabilitazione di HTTPS

Dopo aver abilitato HTTPS, è possibile successivamente disabilitarlo. Per disabilitare HTTPS, seguire questa procedura.

### <a name="step-1-disable-the-feature"></a>Passaggio 1: Disabilitare la funzionalità 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN Standard o Premium di Verizon.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per cui si vuole disabilitare HTTPS.

    ![Pannello Endpoint](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Fare clic su **No** per disabilitare HTTPS e quindi su **Applica**.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Passaggio 2: Attendere la propagazione

Per rendere effettiva la disabilitazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato di "HTTPS personalizzato" nel portale di Azure è impostato su "Disabilitato" e i tre passaggi dell'operazione nel pannello HTTPS dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

![Finestra di dialogo per la disabilitazione di HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per la disabilitazione di HTTPS. Al termine, i tre passaggi dell'operazione vengono visualizzati nel pannello HTTPS dominio personalizzato. All'attivazione di ogni passaggio, sotto di esso vengono visualizzati dettagli aggiuntivi. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Avanzamento dell'operazione | Dettagli dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| 2 Deprovisioning del certificato | Eliminazione del certificato |
| 3 Operazione completata | Il certificato è stato eliminato |

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Viene usato il certificato dei nomi alternativi soggetto (SAN) fornito da DigiCert. Un certificato SAN può proteggere più nomi di dominio completi con un certificato.

2. *È possibile usare il certificato dedicato personale?*
    
    Non attualmente, ma questa possibilità è prevista nel piano d'azione.

3. *Cosa accade se non si riceve il messaggio di verifica del dominio da DigiCert?*

    Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e protezione di un certificato dedicato. Tutti i certificati SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata di server.

5. *È possibile usare la funzionalità HTTPS del dominio personalizzato con la rete CDN di Azure fornita da Akamai?*

    Attualmente questa funzionalità è disponibile solo con la rete CDN di Azure fornita da Verizon. Siamo impegnati nel fornire il supporto di questa funzionalità con la rete CDN di Azure fornita da Akamai nei prossimi mesi.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare un [dominio personalizzato nell'endpoint della rete CDN di Azure](./cdn-map-content-to-custom-domain.md)


