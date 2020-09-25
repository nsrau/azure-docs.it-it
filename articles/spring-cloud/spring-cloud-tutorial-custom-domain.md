---
title: 'Esercitazione: Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud'
description: Come eseguire il mapping di un nome DNS (Distributed Name Service) personalizzato esistente ad Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cd10421ddcf752625b8040e1afa4e7b15f142ce2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885686"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C#

DNS (Domain Name Service) è una tecnica per l'archiviazione dei nomi dei nodi di un'intera rete. Questa esercitazione illustra come eseguire il mapping di un dominio, ad esempio www.contoso.com, usando un record CNAME. Il dominio personalizzato viene protetto con un certificato e viene applicato il certificato TLS (Transport Layer Security), anche noto come SSL (Secure Sockets Layer). 

I certificati crittografano il traffico Web. Questi certificati TLS/SSL possono essere archiviati in Azure Key Vault. 

## <a name="prerequisites"></a>Prerequisiti
* Un'applicazione distribuita in Azure Spring Cloud. Vedere [Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure](spring-cloud-quickstart.md) oppure usare un'app esistente.
* Un nome di dominio con accesso al registro DNS per provider di domini come GoDaddy.
* Un certificato privato (ossia il certificato autofirmato) da un provider di terze parti. Il certificato deve corrispondere al dominio.
* Un'istanza distribuita di [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="import-certificate"></a>Importare il certificato 
Per importare un certificato, è necessario che il file con codifica PEM o PFX sia presente nel disco ed è necessario avere la chiave privata. 

Per caricare il certificato nell'insieme di credenziali delle chiavi:
1. Passare all'istanza di Key Vault.
1. Nel riquadro di spostamento sinistro fare clic su **Certificati**.
1. Nel menu superiore scegliere **Genera/Importa**.
1. Nella finestra di dialogo **Crea un certificato**, in **Metodo di creazione del certificato**, selezionare `Import`.
1. In **Carica il file di certificato** passare alla posizione del certificato e selezionarlo.
1. In **Password** immettere la chiave privata per il certificato.
1. Fare clic su **Crea**.

    ![Importare il certificato 1](./media/custom-dns-tutorial/import-certificate-a.png)

Per concedere ad Azure Spring Cloud l'accesso all'insieme di credenziali delle chiavi prima di importare il certificato:
1. Passare all'istanza di Key Vault.
1. Nel riquadro di spostamento sinistro fare clic su **Criteri di accesso**.
1. Nel menu superiore fare clic su **Aggiungi criterio di accesso**.
1. Immettere le informazioni, fare clic sul pulsante **Aggiungi** e quindi su **Salva** per salvare i criteri di accesso.

| Autorizzazione per il segreto | Autorizzazione del certificato | Selezionare un'entità |
|--|--|--|
| Ottieni, Elenca | Ottieni, Elenca | Azure Spring Cloud Domain-Management |

![Importare il certificato 2](./media/custom-dns-tutorial/import-certificate-b.png)

Si può anche usare l'interfaccia della riga di comando di Azure per concedere ad Azure Spring Cloud l'accesso all'insieme di credenziali delle chiavi.

Ottenere l'ID oggetto tramite il comando seguente.
```
az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId
```

Concedere ad Azure Spring Cloud l'accesso in lettura all'insieme di credenziali delle chiavi, sostituire l'ID oggetto nel comando seguente.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id <object id> --certificate-permissions get list --secret-permissions get list
``` 

Per importare il certificato in Azure Spring Cloud:
1. Passare all'istanza del servizio. 
1. Nel riquadro di spostamento sinistro dell'app selezionare **Impostazioni TLS/SSL**.
1. Quindi fare clic su **Importa certificato dell'insieme di credenziali delle chiavi**.

    ![Importare il certificato](./media/custom-dns-tutorial/import-certificate.png)

Per importare il certificato si può anche usare l'interfaccia della riga di comando di Azure:

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

> [!IMPORTANT] 
> Assicurarsi di concedere ad Azure Spring Cloud l'accesso all'insieme di credenziali delle chiavi prima di eseguire il comando di importazione certificato precedente. Se non è stato fatto, è possibile eseguire il comando seguente per concedere i diritti di accesso.

```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list
``` 

Una volta importato correttamente, il certificato verrà visualizzato nell'elenco **Certificati a chiave privata**.

![Certificato a chiave privata](./media/custom-dns-tutorial/key-certificates.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per visualizzare un elenco di certificati:

```
az spring-cloud certificate list
```

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario comunque associarlo a un dominio specifico. Seguire i passaggi descritti in questo documento nella sezione **Aggiungere il binding SSL**.

## <a name="add-custom-domain"></a>Aggiungere il dominio personalizzato
È possibile usare un record CNAME per eseguire il mapping di un nome DNS personalizzato ad Azure Spring Cloud. 

> [!NOTE] 
> Il record A non è supportato. 

### <a name="create-the-cname-record"></a>Creazione di un record CNAME
Passare al provider DNS e aggiungere un record CNAME per eseguire il mapping del dominio a <service_name>.azuremicroservices.io. Qui <service_name> è il nome dell'istanza di Azure Spring Cloud. Sono supportati il dominio e il sottodominio con caratteri jolly. Dopo aver aggiunto il record CNAME, la pagina dei record DNS sarà simile all'esempio seguente: 

![Pagina dei record DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Eseguire il mapping del dominio personalizzato all'app di Azure Spring Cloud
Se non è stata ancora disponibile un'applicazione in Azure Spring Cloud, seguire le istruzioni riportate in [Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

Passare alla pagina dell'applicazione.

1. Selezionare **Dominio personalizzato**.
2. Quindi selezionare **Aggiungi dominio personalizzato**. 

    ![Dominio personalizzato](./media/custom-dns-tutorial/custom-domain.png)

3. Digitare il nome di dominio completo per cui è stato aggiunto un record CNAME, ad esempio www.contoso.com. Assicurarsi che il tipo di record Hostname sia impostato su CNAME (<service_name>.azuremicroservices.io)
4. Fare clic su **Convalida** per abilitare il pulsante **Aggiungi**.
5. Scegliere **Aggiungi**.

    ![Aggiungere il dominio personalizzato](./media/custom-dns-tutorial/add-custom-domain.png)

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per aggiungere un dominio personalizzato:
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> 
```

Un'app può avere più domini, mentre un dominio può essere mappato solo a un'app. Una volta eseguito il mapping all'app, il dominio personalizzato verrà visualizzato nella tabella corrispondente.

![Tabella del dominio personalizzato](./media/custom-dns-tutorial/custom-domain-table.png)

È anche possibile usare l'interfaccia della riga di comando di Azure per visualizzare un elenco di domini personalizzati:
```
az spring-cloud app custom-domain list --app <app name> 
```

> [!NOTE]
> Se è presente un'etichetta **Non sicuro**, significa che il dominio personalizzato non è ancora associato a un certificato SSL. Qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato riceverà un errore o un avviso.

## <a name="add-ssl-binding"></a>Aggiungere il binding SSL
Nella tabella del dominio personalizzato selezionare **Aggiungi l'associazione SSL**, come illustrato nella figura precedente.  
1. Selezionare il **certificato** o importarlo.
1. Fare clic su **Salva**.

    ![Aggiungere il binding SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per **aggiungere il binding SSL**:
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> 
```

Dopo aver aggiunto correttamente il binding SSL, lo stato del dominio sarà sicuro: **Integro**. 

![Aggiungere il binding SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Applicare HTTPS
Per impostazione predefinita, chiunque può comunque accedere all'app tramite HTTP, ma è possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.

Nel riquadro di spostamento sinistro della pagina dell'app selezionare **Dominio personalizzato**. Quindi impostare **Solo HTTPS** su *True*.

![Aggiungere il binding SSL 3](./media/custom-dns-tutorial/enforce-http.png)

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per imporre HTTPS:
```
az spring-cloud app update -name <app-name> --https-only <true|false> -g <resource group> --service <service-name>
```

Al termine dell'operazione, passare a uno degli URL HTTPS che puntano all'app. Si noti che gli URL HTTP non funzionano.

## <a name="see-also"></a>Vedere anche
* [Cos'è l'insieme di credenziali chiave di Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importare un certificato](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Avviare l'app Spring Cloud tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

