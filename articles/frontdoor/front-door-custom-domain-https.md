---
title: Esercitazione - Configurare HTTPS in un dominio personalizzato per il servizio Frontdoor di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come abilitare e disabilitare HTTPS nella configurazione del servizio Frontdoor di Azure per un dominio personalizzato.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: b99132cceb8981a93a8f1c10ccc488d5806f7254
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050978"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Esercitazione: Configurare HTTPS per un dominio personalizzato di Frontdoor

Questa esercitazione illustra come abilitare il protocollo HTTPS per un dominio personalizzato associato alla frontdoor nella sezione degli host front-end. Usando il protocollo HTTPS nel dominio personalizzato (ad esempio, https:\//www.contoso.com), si assicura che i dati sensibili vengano recapitati in modo sicuro tramite crittografia TLS/SSL in caso di invio su Internet. Quando il Web browser è connesso a un sito Web tramite HTTPS, convalida il certificato di sicurezza del sito Web e verifica che sia emesso da un'autorità di certificazione legittima. Questo processo offre sicurezza e protezione delle applicazioni Web da attacchi.

Il servizio Frontdoor di Azure supporta HTTPS per un nome host predefinito della frontdoor, per impostazione predefinita. Ad esempio, se si crea una frontdoor (ad esempio https:\//contoso.azurefd.net), HTTPS viene abilitato automaticamente per le richieste effettuate a https://contoso.azurefd.net. Tuttavia, dopo l'onboarding del dominio personalizzato 'www.contoso.com' sarà necessario inoltre abilitare HTTPS per questo host front-end.   

Di seguito sono riportati alcuni attributi chiave della funzionalità HTTPS personalizzato.

- Assenza di costi aggiuntivi: non sono previsti costi per l'acquisizione o il rinnovo di certificati o per il traffico HTTPS. 

- Abilitazione semplice: il provisioning è disponibile con un unico clic nel [portale di Azure](https://portal.azure.com). È possibile anche usare l'API REST o altri strumenti per sviluppatori per abilitare la funzionalità.

- Gestione completa dei certificati: tutta la fase di approvvigionamento e gestione di certificati viene gestita automaticamente. Il provisioning e il rinnovo dei certificati vengono eseguiti automaticamente prima della scadenza. Ciò elimina il rischio di interruzione del servizio a causa della scadenza di un certificato.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Abilitare il protocollo HTTPS nel dominio personalizzato
> - Usare un certificato gestito dal servizio Frontdoor di Azure 
> - Usare il certificato personale, vale a dire un certificato SSL personalizzato
> - Convalidare il dominio
> - Disabilitare il protocollo HTTPS nel dominio personalizzato


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di poter completare i passaggi di questa esercitazione, è necessario creare una frontdoor ed eseguire l'onboarding di almeno un dominio personalizzato. Per altre informazioni, vedere [Esercitazione: Aggiungere un dominio personalizzato alla frontdoor](front-door-custom-domain.md).

## <a name="ssl-certificates"></a>Certificati SSL

Per abilitare il protocollo HTTPS per il recapito sicuro del contenuto in un dominio personalizzato di Frontdoor, è necessario usare un certificato SSL. È possibile scegliere di usare un certificato gestito dal servizio Frontdoor di Azure o il certificato personale.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Opzione 1 (predefinita): Usare un certificato gestito da Frontdoor

Quando si usa un certificato gestito dal servizio Frontdoor di Azure, è possibile attivare la funzionalità HTTPS con pochi clic. Le attività di gestione dei certificati, come l'acquisizione e il rinnovo, vengono interamente gestite dal servizio Frontdoor di Azure. Dopo l'abilitazione della funzionalità, il processo viene avviato immediatamente. Se il dominio personalizzato è già mappato all'host front-end predefinito di Frontdoor (`{hostname}.azurefd.net`), non sono necessarie altre azioni. Il servizio Frontdoor elaborerà i passaggi e completerà la richiesta automaticamente. Se il dominio personalizzato è mappato in altro modo, invece, è necessario convalidare la proprietà del dominio tramite posta elettronica.

Per abilitare il protocollo HTTPS in un dominio personalizzato, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare al profilo di **Frontdoor**.

2. Nell'elenco degli host front-end, selezionare il dominio personalizzato per cui si vuole abilitare HTTPS.

3. Nella sezione **HTTPS dominio personalizzato** fare clic su **Abilitato** e selezionare **Frontdoor gestita** come origine del certificato.

4. Fare clic su Save.

5. Procedere a [Convalidare il dominio](#validate-the-domain).


### <a name="option-2-use-your-own-certificate"></a>Opzione 2: Usare un certificato personale

Per abilitare la funzionalità HTTPS, è possibile usare un certificato personale. Questo processo avviene tramite un'integrazione con Azure Key Vault, che consente di archiviare i certificati in modo sicuro. Il servizio Frontdoor di Azure usa questo meccanismo sicuro per ottenere il certificato e richiede alcuni passaggi aggiuntivi. Quando si crea il certificato SSL, è necessario crearlo con un'autorità di certificazione consentita (CA). In caso contrario, se si usa una CA non consentita, la richiesta verrà rifiutata. Per un elenco delle autorità di certificazione consentite, vedere [Autorità di certificazione consentite per abilitare la funzionalità HTTPS personalizzata nel servizio Frontdoor di Azure](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Preparare l'account e il certificato di Azure Key Vault
 
1. Azure Key Vault: è necessario un account Azure Key Vault in esecuzione nella stessa sottoscrizione della frontdoor per cui si vuole abilitare la funzionalità HTTPS personalizzato. Se non si ha un account Azure Key Vault, crearne uno.
 
2. Certificati di Azure Key Vault: è possibile caricare direttamente nell'account Azure Key Vault un certificato già esistente oppure creare un nuovo certificato direttamente tramite Azure Key Vault presso una delle autorità di certificazione (CA) partner con cui Azure Key Vault è integrato.

> [!WARNING]
> </br> -Il servizio Frontdoor di Azure attualmente supporta solo account Key Vault nella stessa sottoscrizione della configurazione della frontdoor. La scelta di un insieme di credenziali delle chiavi in una sottoscrizione diversa da quella della frontdoor genererà un errore.
> </br> -Il servizio Frontdoor di Azure attualmente supporta solo i certificati di Key Vault archiviati nella sezione dei segreti. L'importazione del certificato avrà esito negativo se viene archiviato nella sezione dei certificati invece che nella sezione dei segreti.
> </br> -Il servizio Frontdoor di Azure attualmente supporta solo i certificati caricati con un file PFX **senza** password.

#### <a name="register-azure-front-door-service"></a>Registrare il servizio Frontdoor di Azure

Registrare l'entità servizio per il servizio Frontdoor di Azure come app in Azure Active Directory tramite PowerShell.

1. Se necessario, installare [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell nel computer locale.

2. In PowerShell eseguire questo comando:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-service-access-to-your-key-vault"></a>Concedere al servizio Frontdoor di Azure l'accesso all'insieme di credenziali delle chiavi
 
Concedere al servizio Frontdoor di Azure l'autorizzazione ad accedere ai certificati nella sezione dei segreti nell'account Azure Key Vault.

1. Nell'account Key Vault selezionare **Criteri di accesso** in IMPOSTAZIONI e quindi **Aggiungi nuovo** per creare un nuovo criterio.

2. In **Selezionare un'entità** cercare **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** e scegliere **Microsoft.Azure.Frontdoor**. Fare clic su **Seleziona**.


3. In **Autorizzazioni segrete** selezionare **Recupera** per consentire a Frontdoor di usare queste autorizzazioni per recuperare ed elencare i certificati. 

4. Selezionare **OK**. 

    Il servizio Frontdoor di Azure può ora accedere a questo insieme di credenziali delle chiavi e ai certificati (segreti) in esso archiviati.
 
#### <a name="select-the-certificate-for-azure-front-door-service-to-deploy"></a>Selezionare il certificato per il servizio Frontdoor di Azure da distribuire
 
1. Tornare alla frontdoor nel portale. 

2. Nell'elenco di domini personalizzati selezionare il dominio personalizzato per cui si vuole abilitare il protocollo HTTPS.

    Viene visualizzata la pagina **Dominio personalizzato**.

3. In Tipo di gestione dei certificati selezionare **Usa certificato personale**. 

4. Il servizio Frontdoor di Azure richiede che la sottoscrizione dell'account Key Vault sia identica a quella della frontdoor. Selezionare un insieme di credenziali delle chiavi, un certificato (segreto) e una versione del certificato.

    Il servizio Frontdoor di Azure elenca le informazioni seguenti: 
    - Account Key Vault per l'ID sottoscrizione. 
    - Certificati (segreti) nell'insieme di credenziali delle chiavi selezionato. 
    - Versioni del certificato disponibili. 
 
5. Quando si usa un certificato proprio, la convalida del dominio non è necessaria. Passare ad [Attendere la propagazione](#wait-for-propagation).

## <a name="validate-the-domain"></a>Convalidare il dominio

Se è già in uso un dominio personalizzato mappato all'endpoint personalizzato con un record CNAME record o se si usa un certificato proprio, passare a  
[Il dominio personalizzato è mappato alla frontdoor](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). In caso contrario, se la voce del record CNAME per il dominio non esiste più o se contiene il sottodominio afdverify, passare a [Il dominio personalizzato non è mappato alla frontdoor](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Il dominio personalizzato è mappato alla frontdoor con un record CNAME

Quando viene aggiunto un dominio personalizzato agli host front-end della frontdoor, nella tabella DNS del registrar viene creato un record CNAME per mapparlo al nome host .azurefd.net predefinito della frontdoor. Se il record CNAME esiste già e non contiene il sottodominio afdverify, l'autorità di certificazione DigiCert lo usa per convalidare automaticamente la proprietà del dominio personalizzato. 

Se si usa un certificato proprio, la convalida del dominio non è necessaria.

Il record CNAME deve avere il formato seguente, dove *Nome* è il nome del dominio personalizzato e *Valore* è il nome host .azurefd.net predefinito della frontdoor:

| NOME            | Type  | Valore                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Per altre informazioni sui record CNAME, vedere [Create the CNAME DNS record](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain) (Creare un record DNS CNAME).

Se il record CNAME è nel formato corretto, DigiCert verifica automaticamente il nome di dominio personalizzato e crea un certificato dedicato per il nome di dominio. DigitCert non invia alcun messaggio di verifica e non sarà necessario approvare la richiesta. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza. Passare ad [Attendere la propagazione](#wait-for-propagation). 

La convalida automatica richiede in genere qualche minuto. Se il dominio non viene convalidato entro un'ora, aprire un ticket di supporto.

>[!NOTE]
>Se si ha un record di autorizzazione dell'autorità di certificazione (CAA, Certificate Authority Authorization) con il provider DNS, il record deve includere DigiCert come CA valida. Un record CAA consente ai proprietari di domini di indicare ai propri provider DNS le CA autorizzate a emettere certificati per i loro domini. Se una CA riceve l'ordine di un certificato per un dominio dotato di record CAA ma la CA non è citata come autorità emittente autorizzata in tale record, non deve emettere il certificato per il dominio o il sottodominio. Per informazioni sulla gestione dei record CAA, vedere l'argomento relativo alla [gestione dei record CAA](https://support.dnsimple.com/articles/manage-caa-record/). Per informazioni su uno strumento per i record CAA, vedere [Strumento di supporto per record CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Il dominio personalizzato non è mappato alla frontdoor

Se la voce di record CNAME per l'endpoint non esiste più o contiene il sottodominio afdverify, seguire le istruzioni riportate in questo passaggio.

Dopo l'abilitazione di HTTPS nel dominio personalizzato, la CA DigiCert convalida la proprietà del dominio contattandone il registrante in base alle informazioni sul registrante stesso in [WHOIS](http://whois.domaintools.com/). Per il contatto viene usato l'indirizzo di posta elettronica (impostazione predefinita) o il numero di telefono riportato nella registrazione WHOIS. Prima che la funzionalità HTTPS sia attiva nel dominio personalizzato, è necessario completare la convalida del dominio. Il dominio deve essere approvato entro sei giorni lavorativi. Le richieste non approvate entro sei giorni lavorativi vengono annullate automaticamente. 

![Record WHOIS](./media/front-door-custom-domain-https/whois-record.png)

DigiCert invia un messaggio di verifica anche a indirizzi di posta elettronica aggiuntivi. Se le informazioni del registrante WHOIS sono private, verificare di poter eseguire l'approvazione direttamente da uno degli indirizzi seguenti:

admin@&lt;nome-dominio.com&gt;  
administrator@&lt;nome-dominio.com&gt;  
webmaster@&lt;nome-dominio.com&gt;  
hostmaster@&lt;nome-dominio.com&gt;  
postmaster@&lt;nome-dominio.com&gt;  

Entro pochi minuti si dovrebbe ricevere un messaggio di posta elettronica simile all'esempio seguente, in cui viene chiesto di approvare la richiesta. Se si usa un filtro per la posta indesiderata, aggiungere admin@digicert.com all'elenco degli indirizzi consentiti. Se non si riceve un messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

Facendo clic sul collegamento per l'approvazione, si verrà indirizzati a un modulo di approvazione online. Seguire le istruzioni nel modulo. Sono disponibili due opzioni di verifica:

- È possibile approvare tutti gli ordini futuri effettuati con lo stesso account per lo stesso dominio radice, ad esempio contoso.com. Questo approccio è consigliato se si prevede di aggiungere altri domini personalizzati per lo stesso dominio radice.

- È possibile approvare solo il nome host specifico usato in questa richiesta. Le richieste successive richiedono un'approvazione aggiuntiva.

Dopo l'approvazione, DigiCert completa la creazione del certificato per il nome di dominio personalizzato. Il certificato è valido per un anno e verrà rinnovato automaticamente prima della scadenza.

## <a name="wait-for-propagation"></a>Attendere la propagazione

Dopo la convalida del nome di dominio, per l'attivazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Abilitato** e i quattro passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato è ora pronto per l'uso di HTTPS.

### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per l'abilitazione di HTTPS. Dopo l'abilitazione di HTTPS, i quattro passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. All'attivazione di ogni passaggio, sotto il passaggio in corso vengono visualizzati dettagli aggiuntivi sul passaggio secondario. Non tutti i passaggi secondari verranno eseguiti. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Passaggio dell'operazione | Dettagli del passaggio secondario dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| | La richiesta HTTPS è in fase di invio. |
| | La richiesta HTTPS è stata inviata. |
| 2 Convalida del dominio | Il dominio viene convalidato automaticamente se CNAME è mappato all'host front-end .azurefd.net predefinito della frontdoor. In caso contrario, una richiesta di verifica verrà inviata all'indirizzo di posta elettronica elencato nel record di registrazione del dominio (registrante WHOIS). Verificare il dominio non appena possibile. |
| | La proprietà del dominio è stata convalidata. |
| | La richiesta di convalida della proprietà del dominio è scaduta (probabilmente perché il cliente non ha risposto entro 6 giorni). HTTPS non verrà abilitato nel dominio. * |
| | La richiesta di convalida della proprietà del dominio è stata rifiutata dal cliente. HTTPS non verrà abilitato nel dominio. * |
| 3 Provisioning del certificato | L'autorità di certificazione sta per rilasciare il certificato necessario per abilitare HTTPS nel dominio. |
| | Il certificato è stato rilasciato ed è in fase di distribuzione per la frontdoor. Questo processo potrebbe richiedere fino a 1 ora. |
| | Il certificato è stato distribuito per la frontdoor. |
| 4 Operazione completata | HTTPS è stato abilitato nel dominio. |

\* Questo messaggio viene visualizzato solo se si è verificato un errore. 

Se si verifica un errore prima dell'invio della richiesta, viene visualizzato il messaggio di errore seguente:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Pulire le risorse - Disabilitare il protocollo HTTPS

Nei passaggi precedenti è stato abilitato il protocollo HTTPS nel dominio personalizzato. Se non si vuole più usare il dominio personalizzato con HTTPS, è possibile disabilitare il protocollo HTTPS seguendo questa procedura:

### <a name="disable-the-https-feature"></a>Disabilitare la funzionalità HTTPS 

1. Nel [portale di Azure](https://portal.azure.com) passare alla configurazione del **servizio Frontdoor di Azure**.

2. Nell'elenco degli host front-end fare clic sul dominio personalizzato per il quale si vuole disabilitare HTTPS.

3. Fare clic su **Disabilitato** per disabilitare HTTPS e quindi fare clic su **Salva**.

### <a name="wait-for-propagation"></a>Attendere la propagazione

Per rendere effettiva la disabilitazione della funzionalità HTTPS per il dominio personalizzato possono essere necessarie 6-8 ore. Al termine del processo, lo stato HTTPS personalizzato nel portale di Azure viene impostato su **Disabilitato** e i tre passaggi nella finestra di dialogo del dominio personalizzato sono contrassegnati come completati. Il dominio personalizzato non può più usare HTTPS.

#### <a name="operation-progress"></a>Avanzamento dell'operazione

La tabella seguente illustra l'avanzamento dell'operazione per la disabilitazione di HTTPS. Al termine, i tre passaggi dell'operazione vengono visualizzati nella finestra di dialogo del dominio personalizzato. All'attivazione di ogni passaggio, sotto di esso vengono visualizzati dettagli aggiuntivi. Al completamento di un passaggio, accanto viene visualizzato un segno di spunta verde. 

| Avanzamento dell'operazione | Dettagli dell'operazione | 
| --- | --- |
| 1 Invio della richiesta | Invio della richiesta |
| 2 Deprovisioning del certificato | Eliminazione del certificato |
| 3 Operazione completata | Il certificato è stato eliminato |

## <a name="frequently-asked-questions"></a>Domande frequenti

1. *Chi è il provider di certificati e quale tipo di certificato viene usato?*

    Per il dominio personalizzato viene usato un certificato dedicato/singolo, fornito da Digicert. 

2. *Viene usata una configurazione TLS/SSL basata su IP o su SNI?*

    Il servizio Frontdoor di Azure usa TLS/SSL SNI.

3. *Cosa accade se non si riceve il messaggio di verifica del dominio da DigiCert?*

    Se è disponibile una voce CNAME per il dominio personalizzato che punta direttamente al nome host dell'endpoint (e non si usa il nome del sottodominio afdverify), non viene visualizzato alcun messaggio di verifica del dominio. La convalida viene eseguita automaticamente. In caso contrario, se non è disponibile alcuna voce CNAME e non è stato ricevuto alcun messaggio di posta elettronica entro 24 ore, contattare il supporto tecnico Microsoft.

4. *L'uso di un certificato SAN è meno sicuro rispetto a un certificato dedicato?*
    
    Un certificato SAN applica gli stessi standard di crittografia e sicurezza di un certificato dedicato. Tutti i certificati SSL emessi usano l'algoritmo SHA-256 per la protezione avanzata dei server.

5. *È necessario avere un record di autorizzazione dell'autorità di certificazione presso il provider DNS?*

    No, attualmente un record di autorizzazione dell'autorità di certificazione non è obbligatorio. Se tuttavia se ne ha uno, deve includere DigiCert come CA valida.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
