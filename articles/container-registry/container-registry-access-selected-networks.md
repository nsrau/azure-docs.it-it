---
title: Configurare le regole del firewall del servizio
description: Configurare le regole IP per consentire l'accesso a un registro contenitori di Azure da indirizzi IP pubblici o intervalli di indirizzi selezionati.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984616"
---
# <a name="configure-public-ip-network-rules"></a>Configurare le regole di rete IP pubblico

Per impostazione predefinita, un registro contenitori di Azure accetta connessioni su Internet dagli host in qualsiasi rete. Questo articolo illustra come configurare il registro contenitori per consentire l'accesso solo da indirizzi IP pubblici o intervalli di indirizzi specifici. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e portale di Azure.

Le regole di rete IP vengono configurate nell'endpoint del registro di sistema pubblico. Le regole di rete IP non si applicano agli endpoint privati configurati con [collegamento privato](container-registry-private-link.md)

La configurazione delle regole di accesso IP è disponibile nel livello di servizio del registro contenitori **Premium** . Per informazioni sui limiti e i livelli di servizio del registro di sistema, vedere [livelli di container Registry di Azure](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Accesso da rete pubblica selezionata-interfaccia della riga di comando

### <a name="change-default-network-access-to-registry"></a>Modificare l'accesso di rete predefinito al registro di sistema

Per limitare l'accesso a una rete pubblica selezionata, modificare prima l'azione predefinita per negare l'accesso. Sostituire il nome del registro di sistema con il comando [AZ ACR Update][az-acr-update] seguente:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Aggiungi regola di rete al registro di sistema

Usare il comando [AZ ACR Network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al registro di sistema che consenta l'accesso da un intervallo o un indirizzo IP pubblico. Ad esempio, sostituire il nome del registro contenitori e l'indirizzo IP pubblico di una macchina virtuale in una rete virtuale.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Dopo aver aggiunto una regola, la regola avrà effetto solo alcuni minuti.

## <a name="access-from-selected-public-network---portal"></a>Accesso da rete pubblica selezionata-portale

1. Nel portale passare al registro contenitori.
1. In **Impostazioni**selezionare **rete**.
1. Nella scheda **accesso pubblico** selezionare per consentire l'accesso pubblico dalle **reti selezionate**.
1. In **Firewall**immettere un indirizzo IP pubblico, ad esempio l'indirizzo IP pubblico di una macchina virtuale in una rete virtuale. In alternativa, immettere un intervallo di indirizzi nella notazione CIDR che contiene l'indirizzo IP della macchina virtuale.
1. Selezionare **Salva**.

![Configurare la regola del firewall per il registro contenitori][acr-access-selected-networks]

> [!NOTE]
> Dopo aver aggiunto una regola, la regola avrà effetto solo alcuni minuti.

> [!TIP]
> Facoltativamente, abilitare l'accesso al registro da un computer client locale o da un intervallo di indirizzi IP. Per consentire l'accesso, è necessario l'indirizzo IPv4 pubblico del computer. È possibile trovare questo indirizzo cercando "Qual è l'indirizzo IP" in un browser Internet. L'indirizzo IPv4 del client corrente viene visualizzato automaticamente anche quando si configurano le impostazioni del firewall nella pagina **rete** del portale.

## <a name="disable-public-network-access"></a>Disabilitare l'accesso alla rete pubblica

Per limitare il traffico alle reti virtuali tramite un [collegamento privato](container-registry-private-link.md), disabilitare l'endpoint pubblico nel registro di sistema. La disabilitazione dell'endpoint pubblico sostituisce tutte le configurazioni del firewall.

### <a name="disable-public-access---portal"></a>Disabilitare l'accesso pubblico-portale

1. Nel portale passare al registro contenitori e selezionare **impostazioni > rete**.
1. Nella scheda **accesso pubblico** selezionare **disabilitato**in **Consenti accesso pubblico**. Selezionare quindi **Salva**.

![Disabilitare l'accesso pubblico][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Ripristinare l'accesso predefinito al registro di sistema

Per ripristinare il registro di sistema in modo da consentire l'accesso per impostazione predefinita, aggiornare l'azione predefinita. 

### <a name="restore-default-registry-access---portal"></a>Ripristinare l'accesso predefinito al registro di sistema-portale

1. Nel portale passare al registro contenitori e selezionare **impostazioni > rete**.
1. In **Firewall**selezionare ogni intervallo di indirizzi, quindi selezionare l'icona Elimina.
1. Nella scheda **accesso pubblico** , in **Consenti accesso pubblico**, selezionare **tutte le reti**. Selezionare quindi **Salva**.

![Accesso pubblico da tutte le reti][acr-access-all-networks]

## <a name="next-steps"></a>Passaggi successivi

* Per limitare l'accesso a un registro usando un endpoint privato in una rete virtuale, vedere [configurare il collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).
* Se è necessario configurare regole di accesso al registro di sistema da dietro un firewall client, vedere [configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
