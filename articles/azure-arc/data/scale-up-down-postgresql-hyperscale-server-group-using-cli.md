---
title: Aumentare e ridurre il gruppo di server di database di Azure per PostgreSQL usando l'interfaccia della riga di comando (azdata o kubectl)
description: Aumentare e ridurre il gruppo di server di database di Azure per PostgreSQL usando l'interfaccia della riga di comando (azdata o kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b96f38d04fe3e3cb59fa75424ae588fe0e38f510
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938075"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Aumentare e ridurre il gruppo di server di database di Azure per PostgreSQL usando l'interfaccia della riga di comando (azdata o kubectl)



In alcuni casi potrebbe essere necessario modificare le caratteristiche o la definizione di un gruppo di server. Ad esempio:

- Aumentare o ridurre il numero di Vcore usati da ogni coordinatore o dai nodi del ruolo di lavoro
- Aumentare o ridurre la quantità di memoria utilizzata da ogni coordinatore o dai nodi del ruolo di lavoro

Questa guida illustra come ridimensionare vCore e/o memoria.

La scalabilità verso l'alto o verso il basso delle impostazioni vCore o della memoria del gruppo di server significa che è possibile impostare un valore minimo e/o un massimo per ogni impostazione di vCore e memoria. Se si desidera configurare il gruppo di server per l'utilizzo di un numero specifico di vCore o di una quantità specifica di memoria, è necessario impostare le impostazioni minime uguale alle impostazioni massime.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Mostra la definizione corrente del gruppo di server

Per visualizzare la definizione corrente del gruppo di server e vedere quali sono le impostazioni vCore e Memory correnti, eseguire uno dei comandi seguenti:

### <a name="cli-with-azdata"></a>INTERFACCIA della riga di comando con azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>INTERFACCIA della riga di comando con kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Se è stato creato un gruppo di server di PostgreSQL versione 11, eseguire in `kubectl describe postgresql-11/<server group name>` alternativa.

Restituisce la configurazione del gruppo di server. Se il gruppo di server è stato creato con le impostazioni predefinite, la definizione dovrebbe essere visualizzata come segue:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretare la definizione del gruppo di server

Nella definizione di un gruppo di server, la sezione che contiene le impostazioni di vCore min/max per nodo e la memoria min/max per nodo è la sezione **"pianificazione"** . In questa sezione le impostazioni massime verranno rese permanente in una sottosezione denominata **"limits"** e le impostazioni minime sono rese permanente nella sottosezione denominata **"richieste"**.

Se si impostano le impostazioni minime diverse dalle impostazioni massime, la configurazione garantisce che al gruppo di server siano allocate le risorse richieste se necessario. Non verrà superato il limite impostato.

Le risorse (VCore e Memory) che verranno effettivamente usate dal gruppo di server sono le impostazioni massime e dipendono dai carichi di lavoro e dalle risorse disponibili nel cluster. Se le impostazioni non vengono terminate con un valore massimo, il gruppo di server può utilizzare fino a tutte le risorse allocate dal cluster Kubernetes ai nodi Kubernetes in cui è pianificato il gruppo di server.

Le impostazioni vCore e Memory si applicano a ognuno dei nodi dell'iperscala PostgreSQL (nodo coordinatore e nodi di lavoro). Non è ancora supportata l'impostazione separata delle definizioni del nodo coordinatore e dei nodi del ruolo di lavoro.

In una configurazione predefinita, solo la memoria minima è impostata su 256Mi, poiché si tratta della quantità minima di memoria consigliata per l'esecuzione dell'iperscalabilità di PostgreSQL.

> [!NOTE]
> L'impostazione di un valore minimo non significa che il gruppo di server utilizzerà necessariamente tale minimo. Ciò significa che se il gruppo di server lo richiede, è garantito che sia allocato almeno questo valore minimo. Si consideri, ad esempio, l'impostazione di `--minCpu 2` . Non significa che il gruppo di server utilizzerà almeno 2 Vcore in ogni momento. Invece significa che il gruppo di server può iniziare a usare meno di 2 Vcore se non è necessario che sia molto ed è garantito che sia allocato almeno 2 Vcore se necessario in un secondo momento. Questo implica che il cluster Kubernetes alloca risorse ad altri carichi di lavoro in modo che sia in grado di allocare 2 Vcore al gruppo di server, se necessario.

>[!NOTE]
>Prima di modificare la configurazione del sistema, assicurarsi di acquisire familiarità con il modello di risorsa Kubernetes [qui](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>Scalare verticalmente il gruppo di server

Le impostazioni che si sta per impostare devono essere prese in considerazione nella configurazione impostata per il cluster Kubernetes. Assicurarsi di non impostare i valori che il cluster Kubernetes non sarà in grado di soddisfare. Questo potrebbe causare errori o un comportamento imprevedibile. Ad esempio, se lo stato del gruppo di server rimane nell' _aggiornamento_ dello stato per un lungo periodo di tempo dopo la modifica della configurazione, potrebbe essere un'indicazione che è possibile impostare i parametri seguenti sui valori che il cluster Kubernetes non è in grado di soddisfare. In tal caso, ripristinare la modifica o leggere il _troubleshooting_section.

Si supponga di voler eseguire la scalabilità verticale della definizione del gruppo di server per:

- Min vCore = 2
- VCore max = 4
- Memoria minima = 512Mb
- Memoria max = 1 GB

Usare uno degli approcci seguenti:

### <a name="cli-with-azdata"></a>INTERFACCIA della riga di comando con azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

Il comando viene eseguito correttamente quando Visualizza:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Per informazioni dettagliate su tali parametri, eseguire `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>INTERFACCIA della riga di comando con kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Verrà visualizzato l'editor vi, in cui è possibile spostarsi e modificare la configurazione. Usare il comando seguente per eseguire il mapping dell'impostazione desiderata al nome del campo nella specifica:

- Min vCore = 2-> scheduling\default\resources\requests\cpu
- Max vCore = 4-> scheduling\default\resources\limits\cpu
- Memoria minima = 512Mb-> scheduling\default\resources\requests\cpu
- Memoria max = 1 GB-> scheduling\default\resources\limits\cpu

Se non si ha familiarità con l'editor vi, vedere una descrizione dei comandi che potrebbero essere [necessari:](https://www.computerhope.com/unix/uvi.htm)
- modalità di modifica: `i`
- spostarsi tra le frecce
- modifica _stop: `esc`
- _exit senza salvare: `:qa!`
- _exit dopo il salvataggio: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Mostra la definizione con scalabilità verticale del gruppo di server

Eseguire di nuovo il comando per visualizzare la definizione del gruppo di server e verificare che sia impostato nel modo desiderato:

### <a name="cli-with-azdata"></a>INTERFACCIA della riga di comando con azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>INTERFACCIA della riga di comando con kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Se è stato creato un gruppo di server di PostgreSQL versione 11, eseguire in `kubectl describe postgresql-11/<server group name>` alternativa.


Verrà visualizzata la nuova definizione del gruppo di server:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Ridimensionare il gruppo di server

Per ridurre il numero di server, è necessario eseguire lo stesso comando, ma impostare valori minori per le impostazioni che si desidera ridimensionare. Per rimuovere le richieste e/o i limiti, specificarne il valore come stringa vuota.

## <a name="next-steps"></a>Passaggi successivi

- [Scalabilità orizzontale del gruppo di server di scalabilità orizzontale per database di Azure per PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Espansione di attestazioni di volume permanenti](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
