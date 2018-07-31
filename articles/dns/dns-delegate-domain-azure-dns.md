---
title: 'Esercitazione: Ospitare un dominio e un sottodominio in DNS di Azure'
description: Questa esercitazione illustra come configurare DNS di Azure per ospitare le zone DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205442"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Esercitazione: Ospitare un dominio in DNS di Azure

È possibile usare DNS di Azure per ospitare il dominio DNS e gestire i record DNS. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure. 

Si supponga di acquistare il dominio "contoso.net" da un registrar di nomi di dominio e di creare una zona con il nome "contoso.net" nel servizio DNS di Azure. Il registrar offre al proprietario del dominio la possibilità di configurare i record del server dei nomi (NS) per il dominio. Il registrar archivia i record NS nel dominio padre ".net". Gli utenti Internet di tutto il mondo vengono quindi reindirizzati al dominio nella zona DNS di Azure quando provano a risolvere i record DNS in contoso.net.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una zona DNS
> * Recuperare un elenco di nomi di server
> * Delegare il dominio
> * Verificare il funzionamento della delega


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al portale di Azure.
1. In alto a sinistra selezionare **Crea una risorsa** > **Rete** > **Zona DNS** per aprire la pagina **Crea zona DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Nella pagina **Crea zona DNS** immettere i valori seguenti e quindi selezionare **Crea**:

   | **Impostazione** | **Valore** | **Dettagli** |
   |---|---|---|
   |**Nome**|[nome di dominio] |Nome di dominio che è stato acquistato. In questa esercitazione viene usato contoso.net come esempio.|
   |**Sottoscrizione**|[Sottoscrizione]|Selezionare una sottoscrizione in cui creare la zona.|
   |**Gruppo di risorse**|**Crea nuovo:** contosoRG|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. |
   |**Posizione**|Stati Uniti orientali||

> [!NOTE]
> La località del gruppo di risorse non ha alcun impatto sulla zona DNS. La località della zona DNS è sempre "globale" e non viene visualizzata.

## <a name="retrieve-name-servers"></a>Recuperare i server dei nomi

Prima di poter delegare la zona DNS a DNS di Azure, è necessario conoscere i nomi dei server dei nomi per la zona. DNS Azure alloca i server dei nomi da un pool ogni volta che viene creata una zona.

1. Dopo la creazione della zona DNS, nel riquadro **Preferiti** del portale di Azure selezionare **Tutte le risorse**. Nella pagina **Tutte le risorse** selezionare la zona DNS. Se nella sottoscrizione selezionata sono già presenti numerose risorse, è possibile digitare il nome del dominio nella casella **Filtra per nome** per accedere facilmente al gateway applicazione. 

1. Recuperare i server dei nomi dalla pagina della zona DNS. In questo esempio, alla zona contoso.net sono stati assegnati i server dei nomi *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Elenco dei nomi di server](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

DNS di Azure crea automaticamente nella zona i record NS autorevoli per i server dei nomi assegnati.


## <a name="delegate-the-domain"></a>Delegare il dominio

Dopo la creazione della zona DNS e il recupero dei server dei nomi, è necessario aggiornare il dominio padre con i server dei nomi di DNS di Azure. Ogni registrar prevede i propri strumenti di gestione DNS per modificare i record del server dei nomi per un dominio. Nella pagina di gestione DNS del registrar, modificare i record NS e sostituirli con i server dei nomi di DNS di Azure.

Quando si delega un dominio a DNS di Azure, è necessario usare i server dei nomi forniti da DNS di Azure. È consigliabile usare tutti e quattro server dei nomi, indipendentemente dal nome del dominio. La delega del dominio non richiede un server dei nomi per usare lo stesso dominio di primo livello del dominio locale.

Le deleghe che usano server dei nomi nella propria zona, definiti a volte *server dei nomi personali*, non sono attualmente supportate in DNS di Azure.

## <a name="verify-that-the-delegation-is-working"></a>Verificare il funzionamento della delega

Dopo aver completato la delega, è possibile verificarne il corretto funzionamento usando uno strumento come *nslookup* per eseguire una query sul record SOA (Start of Authority) per la zona. Il record SOA viene creato automaticamente durante la creazione della zona. Potrebbe essere necessario attendere più di 10 minuti dopo il completamento della delega prima che sia possibile verificarne il funzionamento. La propagazione delle modifiche in tutto il sistema DNS può richiedere tempo.

Non è necessario specificare i server dei nomi DNS di Azure. Se la delega è configurata in modo corretto, il normale processo di risoluzione DNS trova automaticamente i server dei nomi.

Da un prompt dei comandi digitare un comando nslookup simile al seguente:

```
nslookup -type=SOA contoso.net
```

Di seguito è riportata una risposta di esempio dal comando precedente:

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile mantenere il gruppo di risorse **contosoRG** se si prevede di eseguire l'esercitazione successiva. In caso contrario, eliminare il gruppo di risorse **contosoRG** per eliminare le risorse create in questa esercitazione. A tale scopo, fare clic sul gruppo di risorse **contosoRG** e quindi su **Elimina gruppo di risorse**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è stato descritto come creare una zona DNS per il dominio ed eseguirne la delega a DNS di Azure. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
