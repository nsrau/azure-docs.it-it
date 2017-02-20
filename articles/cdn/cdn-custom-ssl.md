---
title: Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure | Documentazione Microsoft
description: Informazioni su come abilitare HTTPS nell&quot;endpoint della rete CDN di Azure con un dominio personalizzato.
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
translationtype: Human Translation
ms.sourcegitcommit: aea6f0fedb447e953b9db40342e5091bd35486af
ms.openlocfilehash: 9b80ae8c247480e69025b86fac322166a4d5cb4c


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Il supporto di HTTPS per i domini personalizzati della rete CDN di Azure consente di distribuire contenuto protetto tramite SSL usando il nome del proprio dominio per una maggiore sicurezza dei dati in transito. Il flusso di lavoro end-to-end per abilitare il protocollo HTTPS per il dominio personalizzato è più semplice grazie all'abilitazione con un unico clic e alla gestione completa dei certificati, il tutto senza costi aggiuntivi.

È fondamentale garantire la riservatezza e l'integrità di tutti i dati sensibili delle applicazioni Web in transito. Il protocollo HTTPS garantisce la crittografia dei dati sensibili inviati su Internet. Assicura attendibilità, autenticazione e protezione delle applicazioni Web da attacchi. La rete CDN di Azure supporta attualmente il protocollo HTTPS su un endpoint della rete CDN. Ad esempio, se si crea un endpoint CDN dalla rete CDN di Azure, ad esempio https://contoso.azureedge.net, il protocollo HTTPS è abilitato per impostazione predefinita. A questo punto, con la funzionalità HTTPS per il dominio personalizzato è possibile anche abilitare la distribuzione sicura per un dominio personalizzato, ad esempio https://www.contoso.com. 

Alcuni attributi chiave della funzionalità HTTPS sono:

- Assenza di costi aggiuntivi: non sono previsti costi per l'acquisizione o il rinnovo di certificati o per il traffico HTTPS. L'addebito è relativo solo ai GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. Il provisioning e il rinnovo dei certificati vengono eseguiti automaticamente prima della scadenza. In questo modo si riduce completamente il rischio di interruzione del servizio a causa della scadenza di un certificato.

>[!NOTE] 
>Prima di abilitare il supporto di HTTPS, è necessario avere già definito un [dominio personalizzato della rete CDN di Azure](./cdn-map-content-to-custom-domain.md).

## <a name="step-1-enabling-the-feature"></a>Passaggio 1: abilitazione della funzionalità 

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN Standard o Premium di Verizon.

2. Nell'elenco di endpoint fare clic sull'endpoint contenente il dominio personalizzato.

3. Selezionare il dominio personalizzato per il quale si vuole abilitare la funzionalità HTTPS.

    ![Pannello Endpoint](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Fare clic su **Attiva** per abilitare la funzionalità HTTPS e salvare la modifica.

    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>Passaggio 2: convalida del dominio

>[!IMPORTANT] 
>Prima che la funzionalità HTTPS sia attiva nel dominio personalizzato, è necessario completare la convalida del dominio. Per l'approvazione del dominio sono disponibili 6 giorni lavorativi. La richiesta verrà annullata se non si riceve l'approvazione entro 6 giorni lavorativi.  

Dopo l'abilitazione di HTTPS nel dominio personalizzato, DigiCert, il provider del certificato HTTPS, convaliderà la proprietà del dominio contattando il registrante per il dominio usando le informazioni sul registrante WHOIS tramite posta elettronica (per impostazione predefinita) o telefono. Se le informazioni sul registrante WHOIS sono riservate, DigiCert invierà il messaggio di verifica a **admin@*<nome-proprio-dominio.com>***.

Alla ricezione del messaggio di posta elettronica sono disponibili due opzioni di verifica:

1. È possibile approvare tutti gli ordini futuri eseguiti con lo stesso account per lo stesso dominio radice, ad esempio contoso.com. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati in futuro per lo stesso dominio radice.
 
2. È possibile approvare solo il nome host specifico usato in questa richiesta. Le richieste successive richiederanno un'approvazione aggiuntiva.

    Posta elettronica di esempio:
    
    ![Finestra di dialogo HTTPS personalizzato](./media/cdn-custom-ssl/domain-validation-email-example.png)

Dopo l'approvazione, DigiCert aggiungerà il nome del dominio personalizzato al certificato SAN. Il certificato sarà valido per un anno e verrà rinnovato automaticamente prima della scadenza.

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>Passaggio 3: attendere la propagazione, quindi iniziare a usare la funzionalità

Dopo la convalida del nome di dominio sono necessarie a 6-8 ore per l'attivazione della funzionalità HTTPS nel dominio personalizzato. Al termine del processo, lo stato del protocollo "HTTPS personalizzato" nel portale di Azure verrà impostato su "Abilitato". La funzionalità HTTPS con il dominio personalizzato è ora pronta per l'uso.

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Viene usato il certificato dei nomi alternativi soggetto (SAN) fornito da DigiCert. Un certificato SAN può proteggere più nomi di dominio completi con un certificato.

2. *È possibile usare il certificato dedicato personale?*
    
    Non attualmente, ma questa possibilità è prevista nel piano d'azione.

3. *Cosa accade se non si riceve il messaggio di verifica del dominio da DigiCert?*

    Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e protezione di un certificato dedicato. Tutti i certificati SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata di server.

5. *È possibile usare la funzionalità HTTPS del dominio personalizzato con la rete CDN di Azure fornita da Akamai?*

    Attualmente questa funzionalità è disponibile solo con la rete CDN di Azure fornita da Verizon. Siamo impegnati nel fornire il supporto di questa funzionalità con la rete CDN di Azure fornita da Akamai nei prossimi mesi.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare un [dominio personalizzato nell'endpoint della rete CDN di Azure](./cdn-map-content-to-custom-domain.md)





<!--HONumber=Feb17_HO1-->


