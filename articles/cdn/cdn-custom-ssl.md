---
title: 'Esercitazione: configurare HTTPS in un dominio personalizzato della rete CDN di Azure | Microsoft Docs'
description: Questa esercitazione illustra come abilitare e disabilitare HTTPS nel dominio personalizzato dell'endpoint della rete CDN di Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: f1af388d1f8b9542d196a53cc6c143f9b48e6d5a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394849"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Esercitazione: configurare HTTPS in un dominio personalizzato della rete CDN di Azure

Questa esercitazione illustra come abilitare il protocollo HTTPS per un dominio personalizzato associato a un endpoint della rete CDN di Azure. Usando il protocollo HTTPS nel dominio personalizzato (ad esempio, https:\//www.contoso.com), si garantisce che i dati sensibili vengano recapitati in modo sicuro tramite la crittografia TLS/SSL quando viene inviata attraverso Internet. Quando il Web browser è connesso a un sito Web tramite HTTPS, convalida il certificato di sicurezza del sito Web e verifica che sia emesso da un'autorità di certificazione legittima. Questo processo garantisce la sicurezza e protegge le applicazioni Web dagli attacchi.

Per impostazione predefinita, la rete CDN di Azure supporta HTTPS in un nome host dell'endpoint della rete CDN. Se ad esempio si crea un endpoint della rete CDN, ad esempio https:\//contoso.azureedge.net, HTTPS viene abilitato automaticamente.  

Alcuni degli attributi chiave della funzionalità HTTPS personalizzata sono i seguenti:

- Nessun costo aggiuntivo: non sono previsti costi per l'acquisizione o il rinnovo del certificato e nessun costo aggiuntivo per il traffico HTTPS. Si paga solo per GB in uscita dalla rete CDN.

- Abilitazione semplice: il provisioning con un clic è disponibile dal [portale di Azure](https://portal.azure.com). Per abilitare la funzionalità, è anche possibile usare l'API REST o altri strumenti di sviluppo.

- È disponibile la gestione completa dei certificati: tutti gli approvvigionamenti e la gestione dei certificati vengono gestiti. Viene eseguito automaticamente il provisioning e il rinnovo dei certificati prima della scadenza, che consente di rimuovere i rischi di interruzione del servizio a causa di un certificato in scadenza.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato.
> - Usare un certificato gestito dalla rete CDN 
> - Usare il proprio certificato
> - Convalidare il dominio
> - Disabilitare il protocollo HTTPS nel dominio personalizzato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Prima di completare i passaggi di questa esercitazione, è necessario creare prima un profilo di rete CDN e almeno un endpoint della rete CDN. Per altre informazioni, vedere [Guida introduttiva: creare un profilo e un endpoint](cdn-create-new-endpoint.md)della rete CDN di Azure.

Inoltre, è necessario associare un dominio personalizzato della rete CDN di Azure nell'endpoint della rete CDN. Per altre informazioni, vedere [esercitazione: aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> I certificati gestiti dalla rete CDN non sono disponibili per i domini radice o apice. Se il dominio personalizzato della rete CDN di Azure è un dominio radice o Apex, è necessario usare la funzionalità Bring your own certificate. 
>

---

## <a name="ssl-certificates"></a>Certificati SSL
Per abilitare il protocollo HTTPS per la distribuzione sicura di contenuto in un dominio personalizzato della rete CDN di Azure, è necessario usare un certificato SSL. È possibile scegliere di usare un certificato gestito dalla rete CDN di Azure o usare il proprio certificato.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Opzione 1 (impostazione predefinita): abilitare HTTPS con un certificato gestito dalla rete CDN](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Quando si usa un certificato gestito dalla rete CDN, è possibile accendere la funzionalità HTTPS con pochi clic. La rete CDN di Azure gestisce completamente le attività di gestione dei certificati, ad esempio approvvigionamento e rinnovo. Dopo aver abilitato la funzionalità, il processo viene avviato immediatamente. Se è già stato eseguito il mapping del dominio personalizzato all'endpoint della rete CDN, non è necessaria alcuna azione aggiuntiva. La rete CDN di Azure elaborerà i passaggi e completerà automaticamente la richiesta. Tuttavia, se il dominio personalizzato è mappato in un'altra posizione, è necessario usare la posta elettronica per convalidare la proprietà del dominio.

Per abilitare HTTPS in un dominio personalizzato, attenersi alla procedura seguente:

1. Passare alla [portale di Azure](https://portal.azure.com) per trovare un certificato gestito dalla rete CDN di Azure. Cercare e selezionare profili di rete **CDN**. 

2. Scegliere lo **standard della rete CDN di Azure da Microsoft**, rete **CDN standard di Azure da Akamai**, rete **CDN standard di Azure da Verizon**o profilo **di rete CDN Premium di Azure da Verizon** .

3. Nell'elenco degli endpoint della rete CDN selezionare l'endpoint contenente il dominio personalizzato.

    ![Elenco di endpoint](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Viene visualizzata la pagina **endpoint** .

4. Nell'elenco dei domini personalizzati selezionare il dominio personalizzato per il quale si desidera abilitare HTTPS.

    ![Elenco domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Viene visualizzata la pagina **dominio personalizzato** .

5. In tipo di gestione certificati selezionare rete **CDN gestita**.

6. Selezionare **on** per abilitare HTTPS.

    ![Stato HTTPS del dominio personalizzato](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Procedere con [la convalida del dominio](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Opzione 2: abilitare HTTPS con il proprio certificato](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Questa opzione è disponibile solo con i profili **della rete CDN di Azure di Microsoft** e **della rete CDN di Azure di Verizon** . 
>
 
Per abilitare la funzionalità HTTPS, è possibile usare il proprio certificato. Questo processo viene eseguito tramite un'integrazione con Azure Key Vault, che consente di archiviare i certificati in modo sicuro. La rete CDN di Azure usa questo meccanismo sicuro per ottenere il certificato e richiede alcuni passaggi aggiuntivi. Quando si crea il certificato SSL, è necessario crearlo con un'autorità di certificazione (CA) consentita. In caso contrario, se si usa una CA non consentita, la richiesta verrà rifiutata. Per un elenco delle CA consentite, vedere [autorità di certificazione consentite per abilitare HTTPS personalizzato nella rete CDN di Azure](cdn-troubleshoot-allowed-ca.md). Per la rete **CDN di Azure di Verizon**verrà accettata qualsiasi CA valida. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparare l'account e il certificato di Azure Key Vault
 
1. Azure Key Vault: è necessario disporre di un account di Azure Key Vault in esecuzione nella stessa sottoscrizione del profilo della rete CDN di Azure e degli endpoint della rete CDN per cui si vuole abilitare il protocollo HTTPS personalizzato. Creare un account Azure Key Vault se non è già presente.
 
2. Certificati Azure Key Vault: se si dispone già di un certificato, è possibile caricarlo direttamente nell'account Azure Key Vault oppure creare un nuovo certificato direttamente tramite Azure Key Vault da una delle autorità di certificazione del partner con cui Azure Key Vault si integra. 

### <a name="register-azure-cdn"></a>Registrare la rete CDN di Azure

Registrare la rete CDN di Azure come app nel Azure Active Directory tramite PowerShell.

1. Se necessario, installare [Azure PowerShell](/powershell/azure/install-az-ps) nel computer locale.

2. In PowerShell eseguire il comando seguente:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrare la rete CDN di Azure in PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Concedere l'accesso alla rete CDN di Azure all'insieme di credenziali delle chiavi
 
Concedere l'autorizzazione della rete CDN di Azure per accedere ai certificati (segreti) nell'account Azure Key Vault.

1. Nell'account dell'insieme di credenziali delle chiavi, in impostazioni, selezionare **criteri di accesso**, quindi selezionare **Aggiungi nuovo** per creare un nuovo criterio.

    ![Crea nuovi criteri di accesso](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. In **Seleziona entità**cercare **205478C0-bd83-4e1b-a9d6-db63a3e1e1c8**e scegliere **Microsoft. Azure. CDN**. Fare clic su **Seleziona**.

    ![Impostazioni dei criteri di accesso](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Selezionare **autorizzazioni**per i certificati, quindi selezionare le caselle di controllo per **Get** e **List** per consentire alla rete CDN di eseguire queste autorizzazioni per ottenere ed elencare i certificati.

4. Selezionare **autorizzazioni segrete**, quindi selezionare le caselle di controllo per **Get** e **List** per consentire alla rete CDN di eseguire queste autorizzazioni per ottenere ed elencare i segreti.

5. Fare clic su **OK**. 

    La rete CDN di Azure ora può accedere a questo insieme di credenziali delle chiavi e ai certificati (segreti) archiviati in questo insieme di credenziali delle chiavi.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Selezionare il certificato per la rete CDN di Azure da distribuire
 
1. Tornare al portale della rete CDN di Azure e selezionare il profilo e l'endpoint della rete CDN per cui si vuole abilitare il protocollo HTTPS personalizzato. 

2. Nell'elenco dei domini personalizzati selezionare il dominio personalizzato per il quale si desidera abilitare HTTPS.

    Viene visualizzata la pagina **dominio personalizzato** .

3. In tipo di gestione certificati selezionare **Usa certificato personale**. 

    ![Configurare il certificato](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selezionare un insieme di credenziali delle chiavi, il certificato (segreto) e la versione del certificato.

    Rete CDN di Azure elenca le informazioni seguenti: 
    - Account dell'insieme di credenziali delle chiavi per l'ID sottoscrizione. 
    - Certificati (segreti) nell'insieme di credenziali delle chiavi selezionato. 
    - Versioni dei certificati disponibili. 
 
5. Selezionare **on** per abilitare HTTPS.
  
6. Quando si utilizza il proprio certificato, non è necessaria la convalida del dominio. Continuare ad [attendere la propagazione](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Convalidare il dominio

Se è già in uso un dominio personalizzato di cui è stato eseguito il mapping all'endpoint personalizzato con un record CNAME o si sta usando il proprio certificato, passare a  
[Viene eseguito il mapping del dominio personalizzato all'endpoint](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)della rete CDN. In caso contrario, se la voce di record CNAME per l'endpoint non esiste più o contiene il sottodominio cdnverify, passare a [dominio personalizzato non è mappato all'endpoint](#custom-domain-is-not-mapped-to-your-cdn-endpoint)della rete CDN.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Il dominio personalizzato è mappato all'endpoint della rete CDN da un record CNAME

Quando è stato aggiunto un dominio personalizzato all'endpoint, è stato creato un record CNAME nella tabella DNS del registrar per eseguirne il mapping al nome host dell'endpoint della rete CDN. Se il record CNAME esiste ancora e non contiene il sottodominio cdnverify, la CA DigiCert la usa per convalidare automaticamente la proprietà del dominio personalizzato. 

Se si usa il proprio certificato, la convalida del dominio non è obbligatoria.

Il record CNAME deve avere il formato seguente, dove *nome* è il nome di dominio personalizzato e il *valore* è il nome host dell'endpoint della rete CDN:

| Nome            | Tipo  | Valore                 |
|-----------------|-------|-----------------------|
| < www. contoso. com > | CNAME | contoso.azureedge.net |

Per ulteriori informazioni sui record CNAME, vedere [la pagina relativa alla creazione del record DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Se il record CNAME è nel formato corretto, DigiCert verifica automaticamente il nome di dominio personalizzato e crea un certificato dedicato per il nome di dominio. DigitCert non invierà un messaggio di posta elettronica di verifica e non sarà necessario approvare la richiesta. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza. Continuare ad [attendere la propagazione](#wait-for-propagation). 

La convalida automatica richiede in genere alcune ore. Se il dominio non viene convalidato in 24 ore, aprire un ticket di supporto.

>[!NOTE]
>Se si dispone di un record di autorizzazione dell'autorità di certificazione (CAA) con il provider DNS, deve includere DigiCert come CA valida. Un record CAA consente ai proprietari di dominio di specificare con i relativi provider DNS le autorità di certificazione autorizzate a emettere certificati per il proprio dominio. Se una CA riceve un ordine per un certificato per un dominio che dispone di un record CAA e che l'autorità di certificazione non è elencata come autorità emittente autorizzata, non è consentito l'emissione del certificato a tale dominio o sottodominio. Per informazioni sulla gestione dei record CAA, vedere [gestire i record CAA](https://support.dnsimple.com/articles/manage-caa-record/). Per uno strumento di record CAA, vedere l' [Helper record CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Il dominio personalizzato non è mappato all'endpoint della rete CDN

>[!NOTE]
>Se si usa la rete **CDN di Azure di Akamai**, è necessario configurare il CNAME seguente per abilitare la convalida automatica del dominio. "_acme-Challenge.&lt;nome host del dominio personalizzato&gt;-> CNAME-> &lt;nome host del dominio personalizzato&gt;. ak-acme-challenge.azureedge.net "

Se la voce di record CNAME contiene il sottodominio cdnverify, seguire le istruzioni riportate in questo passaggio.

DigiCert Invia un messaggio di posta elettronica di verifica ai seguenti indirizzi di posta elettronica. Verificare che sia possibile approvare direttamente da uno degli indirizzi seguenti:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Si dovrebbe ricevere un messaggio di posta elettronica in pochi minuti, in modo simile all'esempio seguente, in cui viene chiesto di approvare la richiesta. Se si usa un filtro per la posta indesiderata, aggiungere verification@digicert.com al relativo elenco Consenti. Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.
    
![Posta elettronica di convalida del dominio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando si fa clic sul collegamento di approvazione, si viene indirizzati al modulo di approvazione online seguente: 
    
![Modulo di convalida del dominio](./media/cdn-custom-ssl/domain-validation-form.png)

Seguire le istruzioni nel modulo; sono disponibili due opzioni di verifica:

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice. ad esempio, contoso.com. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Per le richieste successive è necessaria un'approvazione aggiuntiva.

Dopo l'approvazione, DigiCert completa la creazione del certificato per il nome di dominio personalizzato. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

## <a name="wait-for-propagation"></a>Attendi propagazione

Dopo la convalida del nome di dominio, possono essere necessarie fino a 6-8 ore per l'attivazione della funzionalità HTTPS del dominio personalizzato. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **abilitato** e i quattro passaggi dell'operazione nella finestra di dialogo dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

![Finestra di dialogo Abilita HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Stato dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione che si verifica quando si Abilita HTTPS. Dopo aver abilitato HTTPS, nella finestra di dialogo dominio personalizzato verranno visualizzati quattro passaggi dell'operazione. Quando ogni passaggio diventa attivo, i dettagli aggiuntivi del sottopassaggio vengono visualizzati sotto il passaggio durante l'avanzamento. Non si verificheranno tutti questi passaggi. Una volta completato il passaggio, viene visualizzato un segno di spunta verde accanto a esso. 

| Passaggio dell'operazione | Dettagli dell'operazione del sottopassaggio | 
| --- | --- |
| 1 invio della richiesta | Invio della richiesta |
| | È in corso l'invio della richiesta HTTPS. |
| | La richiesta HTTPS è stata inviata correttamente. |
| 2 convalida del dominio | Il dominio viene convalidato automaticamente se è stato eseguito il mapping di CNAME all'endpoint della rete CDN. In caso contrario, verrà inviata una richiesta di verifica al messaggio di posta elettronica elencato nel record di registrazione del dominio (dichiaratore WHOIS). Verificare il dominio il prima possibile. |
| | La proprietà del dominio è stata convalidata. |
| | La richiesta di convalida della proprietà del dominio è scaduta (probabilmente il cliente non ha risposto entro 6 giorni). HTTPS non verrà abilitato nel dominio. * |
| | La richiesta di convalida della proprietà del dominio è stata rifiutata dal cliente. HTTPS non verrà abilitato nel dominio. * |
| 3 provisioning del certificato | L'autorità di certificazione sta attualmente emettendo il certificato necessario per abilitare HTTPS nel dominio. |
| | Il certificato è stato emesso ed è attualmente in fase di distribuzione nella rete CDN. Questa operazione potrebbe richiedere fino a 6 ore. |
| | Il certificato è stato distribuito nella rete CDN. |
| 4 completa | HTTPS è stato abilitato nel dominio. |

\* questo messaggio non viene visualizzato a meno che non si sia verificato un errore. 

Se si verifica un errore prima dell'invio della richiesta, viene visualizzato il messaggio di errore seguente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Pulire le risorse-disabilitare HTTPS

Nei passaggi precedenti è stato abilitato il protocollo HTTPS nel dominio personalizzato. Se non si vuole più usare il dominio personalizzato con HTTPS, è possibile disabilitare HTTPS eseguendo questi passaggi:

### <a name="disable-the-https-feature"></a>Disabilitare la funzionalità HTTPS 

1. Nella [portale di Azure](https://portal.azure.com)cercare e selezionare profili di rete **CDN**. 

2. Scegliere il profilo della rete **CDN standard di Azure da Microsoft**, rete **CDN standard di Azure da Verizon**o rete **CDN Premium di Azure da Verizon** .

3. Nell'elenco degli endpoint selezionare l'endpoint contenente il dominio personalizzato.

4. Scegliere il dominio personalizzato per il quale si desidera disabilitare HTTPS.

    ![Elenco domini personalizzati](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Scegliere **disattivato** per disabilitare HTTPS, quindi selezionare **applica**.

    ![Finestra di dialogo HTTPS personalizzata](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Attendi propagazione

Dopo che la funzionalità HTTPS del dominio personalizzato è stata disabilitata, possono essere necessarie fino a 6-8 ore prima che questa venga applicata. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **disabilitato** e i tre passaggi dell'operazione nella finestra di dialogo dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

![Disabilita la finestra di dialogo HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Stato dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione che si verifica quando si disabilita HTTPS. Dopo aver disattivato HTTPS, nella finestra di dialogo dominio personalizzato vengono visualizzati tre passaggi dell'operazione. Quando ogni passaggio diventa attivo, nel passaggio vengono visualizzati dettagli aggiuntivi. Una volta completato il passaggio, viene visualizzato un segno di spunta verde accanto a esso. 

| Stato dell'operazione | Dettagli operazione | 
| --- | --- |
| 1 invio della richiesta | Invio della richiesta |
| 2 deprovisioning del certificato | Eliminazione del certificato |
| 3 completa | Certificato eliminato |

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Per la rete **CDN di Azure** fornita da Verizon e la rete CDN di **Azure di Microsoft**, viene usato un certificato dedicato/singolo fornito da DigiCert per il dominio personalizzato. 

2. *Si usa il protocollo TLS/SSL basato su IP o SNI?*

    Sia la rete **CDN di Azure di Verizon** che la rete **CDN standard di Azure da Microsoft** usano SNI TLS/SSL.

3. *Cosa accade se non si riceve il messaggio di posta elettronica di verifica del dominio da DigiCert?*

    Se si dispone di una voce CNAME per il dominio personalizzato che punta direttamente al nome host dell'endpoint (e non si usa il nome del sottodominio cdnverify), non si riceverà un messaggio di posta elettronica di verifica del dominio. La convalida viene eseguita automaticamente. In caso contrario, se non è presente una voce CNAME e non è stato ricevuto un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'utilizzo di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN segue gli stessi standard di crittografia e sicurezza di un certificato dedicato. Tutti i certificati SSL emessi usano SHA-256 per migliorare la sicurezza del server.

5. *È necessario un record di autorizzazione dell'autorità di certificazione con il provider DNS?*

    No, al momento non è necessario un record di autorizzazione dell'autorità di certificazione. Tuttavia, se ne è presente uno, deve includere DigiCert come CA valida.

6. *Il 20 giugno 2018 la rete CDN di Azure di Verizon ha iniziato a usare un certificato dedicato con SNI TLS/SSL per impostazione predefinita. Cosa accade ai domini personalizzati esistenti usando i nomi alternativi del soggetto (SAN) e TLS/SSL basato su IP?*

    I domini esistenti verranno migrati gradualmente a un singolo certificato nei prossimi mesi se Microsoft analizza che solo le richieste client di SNI vengono effettuate all'applicazione. Se Microsoft rileva alcune richieste client non SNI effettuate all'applicazione, i domini resteranno nel certificato SAN con TLS/SSL basato su IP. In ogni caso, non vi sarà alcuna interruzione del servizio o il supporto per le richieste client indipendentemente dal fatto che tali richieste siano SNI o non SNI.

7. *Come funzionano i rinnovi di certificati con Bring your own certificate?*

    Per assicurarsi che un certificato più recente venga distribuito nell'infrastruttura PoP, caricare semplicemente il nuovo certificato nell'insieme di credenziali delle credenziali di Azure e quindi nelle impostazioni SSL nella rete CDN di Azure scegliere la versione più recente del certificato e fare clic su Salva. La rete CDN di Azure propagherà quindi il nuovo certificato aggiornato. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato.
> - Usare un certificato gestito dalla rete CDN 
> - Usare il proprio certificato
> - Convalidare il dominio.
> - Disabilitare il protocollo HTTPS nel dominio personalizzato.

Passare all'esercitazione successiva per informazioni su come configurare la memorizzazione nella cache nell'endpoint della rete CDN.

> [!div class="nextstepaction"]
> [Esercitazione: impostare le regole di memorizzazione nella cache della rete CDN di Azure](cdn-caching-rules-tutorial.md)

