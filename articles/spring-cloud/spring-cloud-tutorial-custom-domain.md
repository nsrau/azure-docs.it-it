---
title: 'Esercitazione: Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud'
description: Come eseguire il mapping di un nome DNS (Distributed Name Service) personalizzato esistente ad Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.openlocfilehash: 5b57a2463815d5db1c83d3bc4e8cd56314fb204d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176836"
---
# <a name="map-an-existing-custom-domain-to-azure-spring-cloud"></a>Eseguire il mapping di un dominio personalizzato esistente ad Azure Spring Cloud
DNS (Distributed Name Service) è una tecnica per l'archiviazione dei nomi dei nodi di un'intera rete. Questa esercitazione illustra come eseguire il mapping di un dominio, ad esempio www.contoso.com, usando un record CNAME. Il dominio personalizzato viene protetto con un certificato e viene applicato il certificato TLS (Transport Layer Security), anche noto come SSL (Secure Sockets Layer). 

I certificati crittografano il traffico Web. Questi certificati TLS/SSL possono essere archiviati in Azure Key Vault. 

## <a name="prerequisites"></a>Prerequisiti
* Un'applicazione distribuita in Azure Spring Cloud. Vedere [Avvio rapido: Avviare un'applicazione Azure Spring Cloud esistente con il portale di Azure](spring-cloud-quickstart-launch-app-portal.md) oppure usare un'app esistente.
* Un nome di dominio con accesso al registro DNS per provider di domini come GoDaddy.
* Un certificato privato da un provider di terze parti. Il certificato deve corrispondere al dominio.
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

Per importare il certificato in Azure Spring Cloud:
1. Passare all'istanza del servizio. 
1. Nel riquadro di spostamento sinistro dell'app selezionare **Impostazioni TLS/SSL**.
1. Quindi fare clic su **Importa certificato dell'insieme di credenziali delle chiavi**.

![Importare il certificato](./media/custom-dns-tutorial/import-certificate.png)

Una volta importato correttamente, il certificato verrà visualizzato nell'elenco **Certificato a chiave privata**.

![Certificato a chiave privata](./media/custom-dns-tutorial/key-certificates.png)

>[!IMPORTANT] 
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

Un'app può avere più domini, mentre un dominio può essere mappato solo a un'app. Una volta eseguito il mapping all'app, il dominio personalizzato verrà visualizzato nella tabella corrispondente.

![Tabella del dominio personalizzato](./media/custom-dns-tutorial/custom-domain-table.png)

>[!NOTE]
> Se è presente un'etichetta **Non sicuro**, significa che il dominio personalizzato non è ancora associato a un certificato SSL. Qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato riceverà un errore o un avviso.

## <a name="add-ssl-binding"></a>Aggiungere il binding SSL
Nella tabella del dominio personalizzato selezionare **Aggiungi l'associazione SSL**, come illustrato nella figura precedente.  
1. Selezionare il **certificato** o importarlo.
1. Fare clic su **Salva**.

![Aggiungere il binding SSL](./media/custom-dns-tutorial/add-ssl-binding.png)

Dopo aver aggiunto correttamente il binding SSL, lo stato del dominio sarà sicuro: **Integro**. 

![Aggiungere il binding SSL](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Applicare HTTPS
Per impostazione predefinita, chiunque può comunque accedere all'app tramite HTTP, ma è possibile reindirizzare tutte le richieste HTTP alla porta HTTPS.

Nel riquadro di spostamento sinistro della pagina dell'app selezionare **Dominio personalizzato**. Quindi impostare **Solo HTTPS** su *True*.

![Aggiungere il binding SSL](./media/custom-dns-tutorial/enforce-http.png)

Al termine dell'operazione, passare a uno degli URL HTTPS che puntano all'app. Si noti che gli URL HTTP non funzionano.

## <a name="see-also"></a>Vedere anche
* [Cos'è l'insieme di credenziali chiave di Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Importare un certificato](https://docs.microsoft.com/azure/key-vault/certificate-scenarios#import-a-certificate)
* [Avviare l'app Spring Cloud tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)

