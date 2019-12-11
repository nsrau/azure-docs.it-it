---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 12/09/2019
ms.author: saudas
ms.openlocfilehash: bba4196547bda3d3ddcf3344032de5b9286639a0
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996749"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni delle patch sono più frequenti (in alcuni casi settimanali) e sono destinate esclusivamente a correzioni di bug importanti in una versione secondaria. Le versioni delle patch consentono di risolvere le vulnerabilità della sicurezza o i bug rilevanti con impatto su un numero elevato di clienti e prodotti in esecuzione in ambienti di produzione basati su Kubernetes.

AKS mira a certificare e rilasciare nuove versioni di Kubernetes entro 30 giorni dalla versione upstream, in conformità alla stabilità della versione.

## <a name="kubernetes-versions"></a>Versioni di Kubernetes

Kubernetes usa lo schema di controllo delle versioni [semantico standard](https://semver.org/). Ciò significa che ogni versione di Kubernetes segue questo schema di numerazione:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* Le versioni principali cambiano quando le modifiche API incompatibili o la compatibilità con le versioni precedenti potrebbero essere interrotte.
* Le versioni secondarie cambiano quando vengono apportate modifiche alle funzionalità che sono compatibili con le altre versioni secondarie.
* Le versioni delle patch cambiano quando vengono apportate correzioni di bug compatibili con le versioni precedenti.

Gli utenti devono eseguire l'ultima versione patch della versione secondaria in esecuzione, ad esempio se il cluster di produzione si trova in *1.12.14* e *1.12.15* è la versione patch disponibile più recente disponibile per la serie *1,12* , è consigliabile eseguire l'aggiornamento a *1.12.15* non appena si è in grado di verificare che il cluster sia completamente aggiornato e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

AKS supporta tre versioni secondarie di Kubernetes:

* Versione secondaria corrente rilasciata in AKS (N)
* Due versioni secondarie precedenti. Ogni versione secondaria supportata supporta anche due patch stabili.

Questa operazione è nota come "N-2": (N (ultima versione)-2 (versioni secondarie).

Ad esempio, se AKS introduce *1.15. a* oggi, viene fornito il supporto per le versioni seguenti:

Nuova versione secondaria    |    Elenco versioni supportate
-----------------    |    ----------------------
1.15. a               |    1.15. a, 1.15. b, 1.14. c, 1.14. d, 1.13. e, 1.13. f

Dove ". Letter" è rappresentativo delle versioni patch.

Per informazioni dettagliate sulle comunicazioni relative alle modifiche della versione e alle aspettative, vedere "comunicazioni" di seguito.

Quando viene introdotta una nuova versione secondaria, la versione secondaria più recente e le versioni di patch supportate sono deprecate e rimosse. Se, ad esempio, l'elenco versione supportato corrente è:

```
1.15.a
1.15.b
1.14.c
1.14.d
1.13.e
1.13.f
```

E AKS rilascia 1,16. *questo significa che 1,13.* le versioni (tutte le versioni 1,13) verranno rimosse e non supportate.

> [!NOTE]
> Si noti che se i clienti eseguono una versione di Kubernetes non supportata, verrà richiesto di eseguire l'aggiornamento quando richiede il supporto per il cluster. I cluster che eseguono versioni Kubernetes non supportate non sono coperti dai [criteri di supporto di AKS](https://docs.microsoft.com/azure/aks/support-policies).

Oltre alle versioni precedenti, AKS supporta le due **versioni più recenti** di una determinata versione secondaria. Ad esempio, date le seguenti versioni supportate:

```
Current Supported Version List
------------------------------
1.15.2, 1.15.1, 1.14.5, 1.14.4
```

Se upstream Kubernetes rilasciato 1.15.3 e 1.14.6 e AKS rilascia le versioni delle patch, le versioni meno recenti della patch sono deprecate e rimosse e l'elenco delle versioni supportate diventa:

```
New Supported Version List
----------------------
1.15.*3*, 1.15.*2*, 1.14.*6*, 1.14.*5*
```

### <a name="communications"></a>Comunicazioni

* Per le nuove versioni **secondarie** di Kubernetes
  * Tutti gli utenti vengono informati pubblicamente della nuova versione e della versione che verrà rimossa.
  * Quando viene rilasciata una nuova versione della patch, la versione più recente della patch viene rimossa nello stesso momento.
  * I clienti hanno **60 giorni** dalla data di notifica pubblica per eseguire l'aggiornamento a una versione secondaria supportata.
* Per le nuove versioni di **patch** di Kubernetes
  * A tutti gli utenti viene notificata la nuova versione della patch rilasciata e per eseguire l'aggiornamento alla versione più recente della patch.
  * Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione più recente supportata della patch. Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione di patch supportata prima che venga rimosso il meno recente.

AKS definisce una "versione rilasciata" come le versioni disponibili a livello generale, abilitata in tutte le misurazioni SLO/qualità del servizio e disponibile in tutte le aree. AKS può supportare anche le versioni di anteprima, etichettate in modo esplicito e soggette a termini e condizioni per l'anteprima.

#### <a name="notification-channels-for-aks-changes"></a>Canali di notifica per le modifiche AKS

AKS pubblica gli aggiornamenti regolari dei servizi che riepilogano le nuove versioni di Kubernetes, le modifiche ai servizi e gli aggiornamenti dei componenti che sono stati rilasciati nel servizio su [GitHub](https://github.com/Azure/AKS/releases).

Queste modifiche vengono apportate a tutti i clienti nell'ambito della normale manutenzione offerta come parte del servizio gestito, alcune richiedono aggiornamenti espliciti mentre altre non richiedono alcuna azione.

Le notifiche vengono inviate anche tramite:

* [Note sulla versione di AKS](https://aka.ms/aks/releasenotes)
* Notifiche del portale di Azure
* [Canale di aggiornamento di Azure][azure-update-channel]

### <a name="supported-versions-policy-exceptions"></a>Eccezioni dei criteri delle versioni supportate

AKS si riserva il diritto di aggiungere o rimuovere le versioni nuove/esistenti che sono state identificate in modo da avere uno o più errori di produzione critici o problemi di sicurezza senza preavviso.

È possibile che vengano ignorate le versioni specifiche della patch o che l'implementazione sia stata accelerata a seconda della gravità del bug o del problema di sicurezza.

### <a name="azure-portal-and-cli-default-versions"></a>portale di Azure e le versioni predefinite dell'interfaccia della riga di comando

Quando si distribuisce un cluster del servizio contenitore di Azure nel portale o con l'interfaccia della riga di comando di Azure, per impostazione predefinita viene impostato il valore di N-1 versione secondaria e la patch più recente. Se, ad esempio, AKS supporta *1.15. a*, *1.15. b*, *1.14. c*, *1.14. d*, *1.13. e*e *1.13. f*, la versione predefinita selezionata è *1.14. c*.

AKS sceglie il valore predefinito di N-1 per fornire ai clienti una versione nota, stabile e con patch per impostazione predefinita.

## <a name="list-currently-supported-versions"></a>Elencare le versioni attualmente supportate

Per informazioni sulle versioni attualmente disponibili per la sottoscrizione e l'area geografica, usare il comando [AZ AKS get-versions][az-aks-get-versions] . L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="faq"></a>FAQ

**Cosa accade quando un cliente esegue l'aggiornamento di un cluster Kubernetes con una versione secondaria che non è supportata?**

Se si utilizza la versione *n-3* , l'utente non è supportato e verrà richiesto di effettuare l'aggiornamento. Se l'aggiornamento dalla versione n-3 a n-2 ha esito positivo, si è ora all'interno dei criteri di supporto. ad esempio:

- Se la versione più recente di AKS supportata è *1.13. a* e l'utente si trova in *1.12. b* o versioni precedenti, l'utente non è supportato.
- Se l'aggiornamento da *1.12. b* a *1.13. a* o versione successiva ha esito positivo, si torna all'interno dei criteri di supporto.

Gli aggiornamenti alle versioni precedenti alla finestra supportata di *N-2* non sono supportati. In questi casi, è consigliabile che i clienti creino nuovi cluster AKS e ridistribuiscano i carichi di lavoro con le versioni nella finestra supportata.

**Cosa significa ' fuori supporto '**

"All'esterno del supporto" significa che la versione in esecuzione è esterna all'elenco delle versioni supportate e verrà richiesto di aggiornare il cluster a una versione supportata quando si richiede il supporto. Inoltre, AKS non esegue alcun runtime o altre garanzie per i cluster al di fuori dell'elenco delle versioni supportate.

**Cosa accade quando un cliente ridimensiona un cluster Kubernetes con una versione secondaria che non è supportata?**

Per le versioni secondarie non supportate da AKS, la scalabilità orizzontale o verticale dovrebbe continuare a funzionare, ma è consigliabile eseguire l'aggiornamento per ripristinare il supporto del cluster.

**Un cliente può usare permanentemente una versione di Kubernetes?**

Sì. Tuttavia, se il cluster non si trova in una delle versioni supportate da AKS, il cluster non è in linea con i criteri di supporto di AKS. Azure non aggiorna o elimina automaticamente il cluster.

**Quale versione supporta il piano di controllo se il pool di nodi non si trova in una delle versioni AKS supportate?**

Il piano di controllo deve trovarsi all'interno di una finestra di versioni di tutti i pool di nodi. Per informazioni dettagliate sull'aggiornamento del piano di controllo o dei pool di nodi, vedere la documentazione sull' [aggiornamento dei pool di nodi](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [aggiornare un cluster di Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
