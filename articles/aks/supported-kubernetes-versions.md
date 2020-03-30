---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: 77d64391888957a1697a5823a1485413686682d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593445"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni delle patch sono più frequenti (in alcuni casi settimanali) e sono destinate esclusivamente a correzioni di bug importanti in una versione secondaria. Le versioni delle patch consentono di risolvere le vulnerabilità della sicurezza o i bug rilevanti con impatto su un numero elevato di clienti e prodotti in esecuzione in ambienti di produzione basati su Kubernetes.

AKS mira a certificare e rilasciare nuove versioni di Kubernetes entro 30 giorni da un rilascio a monte, a seconda della stabilità del rilascio.

## <a name="kubernetes-versions"></a>Versioni di Kubernetes

Kubernetes utilizza lo schema di controllo delle versioni [delle versioni semantico](https://semver.org/) standard. Ciò significa che ogni versione di Kubernetes segue questo schema di numerazione:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* Le versioni principali cambiano quando le modifiche dell'API incompatibili o la compatibilità con le versioni precedenti potrebbero essere interrotte.
* Le versioni secondarie cambiano quando vengono apportate modifiche alle funzionalità compatibili con le versioni precedenti rispetto alle altre versioni secondarie.
* Le versioni delle patch cambiano quando vengono eseguite correzioni di bug compatibili con le versioni precedenti.

Gli utenti dovrebbero mirare a eseguire l'ultima versione di patch della versione secondaria in esecuzione, ad esempio se il cluster di produzione è su *1.12.14* e *1.12.15* è l'ultima versione di patch disponibile per la serie *1.12,* è necessario eseguire l'aggiornamento alla *versione 1.12.15* non appena si è in grado di garantire che il cluster sia completamente patchato e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

AKS supporta tre versioni secondarie di Kubernetes:

* La versione secondaria corrente rilasciata in AKS (N)
* Due versioni secondarie precedenti. Ogni versione secondaria supportata supporta anche due patch stabili.

Questo è noto come "N-2": (N (Ultima versione) - 2 (versioni secondarie)).

Ad esempio, se AKS introduce *1.15.a* oggi, viene fornito il supporto per le seguenti versioni:

Nuova versione secondaria    |    Elenco versioni supportate
-----------------    |    ----------------------
1.15.a               |    1.15.a, 1.15.b, 1.14.c, 1.14.d, 1.13.e, 1.13.f

Dove ".letter" è rappresentativo delle versioni delle patch.

Per informazioni dettagliate sulle comunicazioni relative alle modifiche di versione e alle aspettative, vedere "Comunicazioni" di seguito.

Quando viene introdotta una nuova versione secondaria, la versione secondaria meno recente e le versioni di patch supportate sono deprecate e rimosse. Ad esempio, se l'elenco delle versioni supportate corrente è:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E AKS rilascia 1.16. *, ciò significa che la 1.13.* versioni 1.13) verranno rimosse e non supportate.

> [!NOTE]
> Si prega di notare che se i clienti eseguono una versione Kubernetes non supportata, verrà chiesto di eseguire l'aggiornamento quando richiedono il supporto per il cluster. I cluster che eseguono versioni Kubernetes non supportate non sono coperti dai criteri di [supporto AKS.](https://docs.microsoft.com/azure/aks/support-policies)

Oltre a quanto sopra sulle versioni secondarie, AKS supporta le due ultime versioni di **patch** di una determinata versione secondaria. Ad esempio, date le seguenti versioni supportate:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se Upstream Kubernetes rilasciato 1.15.3 e 1.14.6 e AKS rilascia tali versioni di patch, le versioni di patch meno recenti sono deprecate e rimosse e l'elenco delle versioni supportate diventa:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicazioni

* Per le nuove versioni **secondarie** di Kubernetes
  * Tutti gli utenti ricevono una notifica pubblica della nuova versione e di quale versione verrà rimossa.
  * Quando viene rilasciata una nuova versione della patch, la versione di patch meno recente viene rimossa contemporaneamente.
  * I clienti hanno **30 giorni** dalla data di notifica pubblica per l'aggiornamento a una versione secondaria supportata.
* Per le nuove versioni delle **patch** di Kubernetes
  * Tutti gli utenti vengono informati della nuova versione della patch rilasciata e dell'aggiornamento all'ultima versione della patch.
  * Gli utenti hanno **30 giorni** di tempo per eseguire l'aggiornamento a una versione di patch più recente e supportata prima che venga rimossa la versione meno recente.

AKS definisce una "versione rilasciata" come le versioni generalmente disponibili, abilitate in tutte le misurazioni SLO / Qualità del servizio e disponibili in tutte le regioni. AKS può anche supportare versioni di anteprima che sono esplicitamente etichettate e soggette a termini e condizioni di anteprima.

#### <a name="notification-channels-for-aks-changes"></a>Canali di notifica per le modifiche AKSNotification channels for AKS changes

AKS pubblica aggiornamenti regolari del servizio che riepilogano le nuove versioni di Kubernetes, le modifiche ai servizi e gli aggiornamenti dei componenti rilasciati nel servizio su [GitHub](https://github.com/Azure/AKS/releases).

Queste modifiche vengono sottoposte a rollini a tutti i clienti come parte della manutenzione regolare offerta come parte del servizio gestito, alcune richiedono aggiornamenti espliciti, mentre altre non richiedono alcuna azione.

Le notifiche vengono inviate anche tramite:

* [Note sulla versione di AKS](https://aka.ms/aks/releasenotes)
* Notifiche del portale di Azure
* [Canale di aggiornamento di AzureAzure update channel][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Eccezioni dei criteri di versioni supportateSupported Version Policy Exceptions

AKS si riserva il diritto di aggiungere o rimuovere versioni nuove/esistenti che sono state identificate per avere uno o più critici bug di impatto sulla produzione o problemi di sicurezza senza preavviso.

Rilasci di patch specifici possono essere ignorati o implementazione accelerata a seconda della gravità del bug o del problema di sicurezza.

### <a name="azure-portal-and-cli-default-versions"></a>Versioni predefinite del portale di Azure e dell'interfaccia della riga di comandoAzure portal and CLI default versions

Quando si distribuisce un cluster AKS nel portale o con l'interfaccia della riga di comando di Azure, il cluster viene impostato per impostazione predefinita sulla versione secondaria N-1 e sulla patch più recente. Ad esempio, se AKS supporta *1.15.a*, *1.15.b*, *1.14.c*, *1.14.d*, *1.13.e*e *1.13.f*, la versione predefinita selezionata è *1.14.c*.

AKS sceglie l'impostazione predefinita N-1 per fornire ai clienti una versione nota, stabile e con patch per impostazione predefinita.

## <a name="list-currently-supported-versions"></a>Elencare le versioni attualmente supportate

Per scoprire quali versioni sono attualmente disponibili per la sottoscrizione e l'area in uso, usare il comando [az servizio Azure Kubernetes get-versions][az-aks-get-versions]. L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>Domande frequenti

**Cosa accade quando un cliente esegue l'aggiornamento di un cluster Kubernetes con una versione secondaria che non è supportata?**

Se siete sulla versione *n-3,* si è al di fuori del supporto e verrà chiesto di eseguire l'aggiornamento. Se l'aggiornamento dalla versione n-3 a n-2 ha esito positivo, ora rientra nelle nostre norme di supporto. Ad esempio:

- Se la versione AKS supportata meno recente è *1.13.a* e si utilizza *1.12.b* o versione precedente, si è al di fuori del supporto.
- Se l'aggiornamento da *1.12.b* a *1.13.a* o superiore ha esito positivo, si è tornati all'interno delle nostre politiche di supporto.

Gli aggiornamenti a versioni precedenti alla finestra supportata di *N-2* non sono supportati. In questi casi, è consigliabile che i clienti creino nuovi cluster AKS e ridistribuiscano i carichi di lavoro con le versioni nella finestra supportata.

**Cosa significa "Fuori dal supporto"**

"Fuori supporto" indica che la versione in esecuzione non rientra nell'elenco delle versioni supportate e verrà chiesto di aggiornare il cluster a una versione supportata quando si richiede il supporto. Inoltre, AKS non fornisce alcuna runtime o altre garanzie per i cluster al di fuori dell'elenco delle versioni supportate.

**Cosa accade quando un cliente ridimensiona un cluster Kubernetes con una versione secondaria che non è supportata?**

Per le versioni secondarie non supportate da AKS, la scalabilità in entrata o in uscita dovrebbe continuare a funzionare, ma è consigliabile eseguire l'aggiornamento per riportare il cluster in supporto.

**Un cliente può usare permanentemente una versione di Kubernetes?**

Sì. Tuttavia, se il cluster non si trova in una delle versioni supportate da AKS, il cluster non è af. Azure non aggiorna o elimina automaticamente il cluster.

**Quale versione supporta il piano di controllo se il pool di nodi non è in una delle versioni AKS supportate?**

Il piano di controllo deve trovarsi all'interno di una finestra di versioni da tutti i pool di nodi. Per informazioni dettagliate sull'aggiornamento del piano di controllo o dei pool di nodi, visitare la documentazione [sull'aggiornamento dei pool di nodi.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
