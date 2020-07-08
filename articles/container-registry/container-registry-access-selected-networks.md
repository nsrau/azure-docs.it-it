---
title: Configurare l'accesso pubblico al registro
description: Configurare le regole IP per consentire l'accesso a un registro contenitori di Azure da indirizzi o intervalli di indirizzi IP pubblici selezionati.
ms.topic: article
ms.date: 05/19/2020
ms.openlocfilehash: dc0514fbe7d3e01914965cee5dc547172d4435a4
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83702079"
---
# <a name="configure-public-ip-network-rules"></a>Configurare le regole di rete per IP pubblico

Per impostazione predefinita, un registro contenitori di Azure accetta le connessioni Internet da host in qualsiasi rete. Questo articolo illustra come configurare il registro contenitori per consentire l'accesso solo da indirizzi o intervalli di indirizzi IP pubblici specifici. Vengono forniti i passaggi equivalenti usando l'interfaccia della riga di comando di Azure e il portale di Azure.

Le regole di rete IP vengono configurate nell'endpoint del registro di sistema pubblico. Le regole di rete IP non si applicano agli endpoint privati configurati con [collegamento privato](container-registry-private-link.md)

La configurazione delle regole di accesso IP è disponibile per il livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio del registro e sui limiti, vedere [livelli di Registro Azure Container](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Accesso da rete pubblica selezionata - Interfaccia della riga di comando

### <a name="change-default-network-access-to-registry"></a>Modificare la regola predefinita di accesso al registro

Per limitare l'accesso a una rete pubblica selezionata, modificare innanzitutto l'azione predefinita per negare l'accesso. Sostituire il nome del registro con nel seguente comando [az acr update][az-acr-update]:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Aggiungere una regola di rete al registro

Usare il comando [az acr network-rule add][az-acr-network-rule-add] per aggiungere una regola di rete al registro che consenta l'accesso da un indirizzo o intervallo di indirizzi IP pubblici. Ad esempio, sostituire il nome del registro contenitori e l'indirizzo IP pubblico di una macchina virtuale in una rete virtuale.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> Dopo aver aggiunto una regola, questa avrà effetto dopo alcuni minuti.

## <a name="access-from-selected-public-network---portal"></a>Accesso da rete pubblica selezionata - Portale

1. Nel portale passare al registro contenitori.
1. In **Impostazioni** selezionare **Rete**.
1. Nella scheda **Accesso pubblico** selezionare questa opzione per consentire l'accesso pubblico da **Reti selezionate**.
1. In **Firewall** immettere un indirizzo IP pubblico, ad esempio l'indirizzo IP pubblico di una macchina virtuale in una rete virtuale. In alternativa, immettere un intervallo di indirizzi nella notazione CIDR che contenga l'indirizzo IP della macchina virtuale.
1. Selezionare **Salva**.

![Configurare una regola del firewall per il registro contenitori][acr-access-selected-networks]

> [!NOTE]
> Dopo aver aggiunto una regola, questa avrà effetto dopo alcuni minuti.

> [!TIP]
> Facoltativamente, abilitare l'accesso al registro da un computer client locale o da un intervallo di indirizzi IP. Per consentire l'accesso, è necessario l'indirizzo IPv4 pubblico del computer. È possibile trovare questo indirizzo cercando "Qual è il mio indirizzo IP" in un browser Internet. L'indirizzo IPv4 del client corrente viene visualizzato automaticamente anche quando si configurano le impostazioni del firewall nella pagina **Rete** del portale.

## <a name="disable-public-network-access"></a>Disabilitare l'accesso alla rete pubblica

Facoltativamente, disabilitare l'endpoint pubblico nel registro di sistema. La disabilitazione dell'endpoint pubblico sostituisce tutte le configurazioni del firewall. Ad esempio, potrebbe essere necessario disabilitare l'accesso pubblico a un registro protetto in una rete virtuale usando un [collegamento privato](container-registry-private-link.md).

### <a name="disable-public-access---cli"></a>Disabilitare l'accesso pubblico - Interfaccia della riga di comando

Per disabilitare l'accesso pubblico usando l'interfaccia della riga di comando di Azure, eseguire [az acr update][az-acr-update] e impostare `--public-network-enabled` su `false`. 

> [!NOTE]
> L'argomento `public-network-enabled` richiede l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled false
```

### <a name="disable-public-access---portal"></a>Disabilitare l'accesso pubblico - Portale

1. Nel portale passare al registro contenitori e selezionare **Impostazioni > Rete**.
1. Nella scheda **Accesso pubblico** in **Consenti l'accesso alla rete pubblica** selezionare **Disabilitato**. Selezionare quindi **Salva**.

![Disabilitare l'accesso pubblico][acr-access-disabled]


## <a name="restore-public-network-access"></a>Ripristinare l'accesso alla rete pubblica

Per abilitare nuovamente l'endpoint pubblico, aggiornare le impostazioni di rete per consentire l'accesso pubblico. L'abilitazione dell'endpoint pubblico sostituisce tutte le configurazioni del firewall. 

### <a name="restore-public-access---cli"></a>Ripristinare l'accesso pubblico - Interfaccia della riga di comando

Eseguire [az acr update][az-acr-update] e impostare `--public-network-enabled` su `true`. 

> [!NOTE]
> L'argomento `public-network-enabled` richiede l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. 

```azurecli
az acr update --name myContainerRegistry --public-network-enabled true
```

### <a name="restore-public-access---portal"></a>Ripristinare l'accesso pubblico - Portale

1. Nel portale passare al registro contenitori e selezionare **Impostazioni > Rete**.
1. Nella scheda **Accesso pubblico** in **Consenti l'accesso alla rete pubblica** selezionare **Tutte le reti**. Selezionare quindi **Salva**.

![Accesso pubblico da tutte le reti][acr-access-all-networks]

## <a name="next-steps"></a>Passaggi successivi

* Per limitare l'accesso a un registro usando un endpoint privato in una rete virtuale, vedere [Configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).
* Per configurare regole di accesso al registro da dietro un firewall del client, vedere [Configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](container-registry-firewall-access-rules.md).

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
