---
title: 'Esercitazione: Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud'
description: Come eseguire il mapping di un nome DNS (Distributed Name Service) personalizzato esistente ad Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d06a6eb8b504f2c5dd09de70d79f50a3ed5d89a3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844728"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java ✔️ C#

DNS (Domain Name Service) è una tecnica per l'archiviazione dei nomi dei nodi di un'intera rete. Questa esercitazione illustra come eseguire il mapping di un dominio, ad esempio www.contoso.com, usando un record CNAME. Il dominio personalizzato viene protetto con un certificato e viene applicato il certificato TLS (Transport Layer Security), anche noto come SSL (Secure Sockets Layer). 

I certificati crittografano il traffico Web. Questi certificati TLS/SSL possono essere archiviati in Azure Key Vault. 

## <a name="prerequisites"></a>Prerequisiti
* Un'applicazione distribuita in Azure Spring Cloud. Vedere [Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure](spring-cloud-quickstart.md) oppure usare un'app esistente.
* Un nome di dominio con accesso al registro DNS per provider di domini come GoDaddy.
* Un certificato privato (ossia il certificato autofirmato) da un provider di terze parti. Il certificato deve corrispondere al dominio.
* Un'istanza distribuita di [Azure Key Vault](../key-vault/general/overview.md)

## <a name="import-certificate"></a>Importare il certificato
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Preparare il file del certificato in formato PFX (facoltativo)
Azure Key Vault supporta l'importazione di un certificato privato in formato PEM e PFX. Se il file PEM ottenuto dal provider di certificati non funziona nella sezione seguente: [salvare il certificato in Key Vault](#save-certificate-in-key-vault) e seguire questa procedura per generare un PFX per Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Unire i certificati intermedi

Se l'autorità di certificazione offre più certificati nella catena di certificati, è necessario unire i certificati nell'ordine.

A tale scopo, aprire ogni certificato ricevuto in un editor di testo.

Creare un file per il certificato unito denominato _mergedcertificate.crt_. In un editor di testo copiare il contenuto di ogni certificato nel file. L'ordine dei certificati deve corrispondere all'ordine nella catena di certificati, che inizia con il certificato dell'utente e termina con il certificato radice. Sarà simile a quanto indicato nell'esempio seguente:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>Esportare il certificato in un file PFX

Esportare il certificato TLS/SSL unito con la chiave privata con cui è stata generata la richiesta del certificato.

Se è stato usato OpenSSL per generare la richiesta del certificato, è stato creato un file di chiave privata. Per esportare il certificato in un file PFX, eseguire il comando seguente. Sostituire i segnaposto _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ con i percorsi della chiave privata e del file di certificato unito.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando richiesto, definire una password di esportazione. Questa password verrà usata durante il caricamento del certificato TLS/SSL in Azure Key Vault più avanti.

se è stato usato IIS o _Certreq.exe_ per generare la richiesta di certificato, installare il certificato nel computer locale e quindi [esportarlo in un file PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Salvare il certificato in Key Vault
Per importare un certificato, è necessario che il file con codifica PEM o PFX sia presente nel disco ed è necessario avere la chiave privata. 
#### <a name="portal"></a>[Portale](#tab/Azure-portal)
Per caricare il certificato nell'insieme di credenziali delle chiavi:
1. Passare all'istanza di Key Vault.
1. Nel riquadro di spostamento sinistro fare clic su **Certificati**.
1. Nel menu superiore scegliere **Genera/Importa**.
1. Nella finestra di dialogo **Crea un certificato**, in **Metodo di creazione del certificato**, selezionare `Import`.
1. In **Carica il file di certificato** passare alla posizione del certificato e selezionarlo.
1. In **Password** immettere la chiave privata per il certificato.
1. Fare clic su **Crea**.

    ![Importare il certificato 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Concedere al servizio Azure Spring Cloud l'accesso all'insieme di credenziali delle chiavi

Prima di importare il certificato, è necessario concedere ad Azure Spring Cloud l'accesso all'insieme di credenziali delle chiavi:
#### <a name="portal"></a>[Portale](#tab/Azure-portal)
1. Passare all'istanza di Key Vault.
1. Nel riquadro di spostamento sinistro fare clic su **Criteri di accesso**.
1. Nel menu superiore fare clic su **Aggiungi criterio di accesso**.
1. Immettere le informazioni, fare clic sul pulsante **Aggiungi** e quindi su **Salva** per salvare i criteri di accesso.

| Autorizzazione per il segreto | Autorizzazione del certificato | Selezionare un'entità |
|--|--|--|
| Ottieni, Elenca | Ottieni, Elenca | Azure Spring Cloud Domain-Management |

![Importare il certificato 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Concedere ad Azure Spring Cloud l'accesso in lettura all'insieme di credenziali delle chiavi, sostituire `<key vault resource group>` e `<key vault name>` nel comando seguente.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importare il certificato in Azure Spring Cloud
#### <a name="portal"></a>[Portale](#tab/Azure-portal)
1. Passare all'istanza del servizio. 
1. Nel riquadro di spostamento sinistro dell'app selezionare **Impostazioni TLS/SSL**.
1. Quindi fare clic su **Importa certificato dell'insieme di credenziali delle chiavi**.

    ![Importare il certificato](./media/custom-dns-tutorial/import-certificate.png)

1. Una volta importato correttamente, il certificato verrà visualizzato nell'elenco **Certificati a chiave privata**.

    ![Certificato a chiave privata](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Per visualizzare un elenco di certificati importati:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Per proteggere un dominio personalizzato con questo certificato, è necessario comunque associarlo a un dominio specifico. Seguire la procedura illustrata nella sezione [Aggiungere il binding SSL](#add-ssl-binding).

## <a name="add-custom-domain"></a>Aggiungere il dominio personalizzato
È possibile usare un record CNAME per eseguire il mapping di un nome DNS personalizzato ad Azure Spring Cloud. 

> [!NOTE] 
> Il record A non è supportato. 

### <a name="create-the-cname-record"></a>Creazione di un record CNAME
Passare al provider DNS e aggiungere un record CNAME per eseguire il mapping del dominio a <service_name>.azuremicroservices.io. Qui <service_name> è il nome dell'istanza di Azure Spring Cloud. Sono supportati il dominio e il sottodominio con caratteri jolly. Dopo aver aggiunto il record CNAME, la pagina dei record DNS sarà simile all'esempio seguente: 

![Pagina dei record DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Eseguire il mapping del dominio personalizzato all'app di Azure Spring Cloud
Se non è stata ancora disponibile un'applicazione in Azure Spring Cloud, seguire le istruzioni riportate in [Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal?branch=master).

#### <a name="portal"></a>[Portale](#tab/Azure-portal)
Passare alla pagina dell'applicazione.

1. Selezionare **Dominio personalizzato**.
2. Quindi selezionare **Aggiungi dominio personalizzato**. 

    ![Dominio personalizzato](./media/custom-dns-tutorial/custom-domain.png)

3. Digitare il nome di dominio completo per cui è stato aggiunto un record CNAME, ad esempio www.contoso.com. Assicurarsi che il tipo di record Hostname sia impostato su CNAME (<service_name>.azuremicroservices.io)
4. Fare clic su **Convalida** per abilitare il pulsante **Aggiungi**.
5. Scegliere **Aggiungi**.

    ![Aggiungere il dominio personalizzato](./media/custom-dns-tutorial/add-custom-domain.png)

Un'app può avere più domini, mentre un dominio può essere mappato solo a un'app. Una volta eseguito il mapping all'app, il dominio personalizzato verrà visualizzato nella tabella corrispondente.

![Tabella del dominio personalizzato](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Per visualizzare l'elenco di domini personalizzati:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Se è presente un'etichetta **Non sicuro**, significa che il dominio personalizzato non è ancora associato a un certificato SSL. Qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato riceverà un errore o un avviso.

## <a name="add-ssl-binding"></a>Aggiungere il binding SSL

#### <a name="portal"></a>[Portale](#tab/Azure-portal)
Nella tabella del dominio personalizzato selezionare **Aggiungi l'associazione SSL**, come illustrato nella figura precedente.  
1. Selezionare il **certificato** o importarlo.
1. Fare clic su **Salva**.

    ![Aggiungere il binding SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Dopo aver aggiunto correttamente il binding SSL, lo stato del dominio sarà sicuro: **Integro**. 

![Aggiungere il binding SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Applicare HTTPS
Per impostazione predefinita, chiunque può comunque accedere all'app tramite HTTP, ma è possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.
#### <a name="portal"></a>[Portale](#tab/Azure-portal)
Nel riquadro di spostamento sinistro della pagina dell'app selezionare **Dominio personalizzato**. Quindi impostare **Solo HTTPS** su *True*.

![Aggiungere il binding SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Al termine dell'operazione, passare a uno degli URL HTTPS che puntano all'app. Si noti che gli URL HTTP non funzionano.

## <a name="see-also"></a>Vedere anche
* [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)
* [Importare un certificato](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Avviare l'app Spring Cloud tramite l'interfaccia della riga di comando di Azure](./spring-cloud-quickstart.md)
