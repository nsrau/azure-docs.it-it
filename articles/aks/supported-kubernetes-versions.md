---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: bf86af16ce330be7119ecbeb24cedbcd31282735
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034036"
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
  1.13.7
```

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* Le versioni principali cambiano quando le modifiche API incompatibili o la compatibilità con le versioni precedenti potrebbero essere interrotte.
* Le versioni secondarie cambiano quando vengono apportate modifiche alle funzionalità che sono compatibili con le altre versioni secondarie.
* Le versioni delle patch cambiano quando vengono apportate correzioni di bug compatibili con le versioni precedenti.

In generale, gli utenti devono eseguire l'ultima versione patch della versione secondaria in esecuzione, ad esempio se il cluster di produzione si trova in *1.13.6* e *1.13.7* è la versione patch disponibile più recente disponibile per la serie *1,13* . è consigliabile eseguire l'aggiornamento a *1.13.7* non appena si è in grado di verificare che il cluster sia completamente aggiornato e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

Il servizio Azure Kubernetes supporta quattro versioni secondarie di Kubernetes:

* Versione secondaria corrente rilasciata in AKS (N)
* Tre versioni secondarie precedenti. Ogni versione secondaria supportata supporta anche due patch stabili.

Questa operazione è nota come "N-3"-(N (ultima versione)-3 (versioni secondarie).

Se, ad esempio, AKS introduce *1.13. x* oggi, viene fornito il supporto per le versioni seguenti:

Nuova versione secondaria    |    Elenco versioni supportate
-----------------    |    ----------------------
1.13.x               |    1.12. a, 1.12. b, 1.11. a, 1.11. b, 1.10. a, 1.10. b

Dove "x" e ". a" e ". b" sono versioni di patch rappresentative.

Per informazioni dettagliate sulle comunicazioni relative alle modifiche della versione e alle aspettative, vedere "comunicazioni" di seguito.

Quando viene introdotta una nuova versione secondaria, la versione secondaria più recente e le versioni di patch supportate sono deprecate e rimosse. Se, ad esempio, l'elenco versione supportato corrente è:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

E AKS rilasciano 1.13. x, ciò significa che le versioni 1.9. x (tutte le versioni 1,9) verranno rimosse e non supportate.

> [!NOTE]
> Si noti che se i clienti eseguono una versione di Kubernetes non supportata, verrà richiesto di eseguire l'aggiornamento quando richiede il supporto per il cluster. I cluster che eseguono versioni Kubernetes non supportate non sono coperti dai [criteri di supporto di AKS](https://docs.microsoft.com/azure/aks/support-policies).


Oltre alle versioni precedenti, AKS supporta le due versioni più recenti di unadata versione secondaria. Ad esempio, date le seguenti versioni supportate:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Se upstream Kubernetes rilasciato 1.12.3 e 1.11.6 e AKS rilascia le versioni delle patch, le versioni meno recenti della patch sono deprecate e rimosse e l'elenco delle versioni supportate diventa:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> I clienti non devono aggiungere la creazione di un cluster, CI o altri processi automatici a versioni patch specifiche. 

### <a name="communications"></a>Comunicazioni

* Per le nuove versioni secondarie di Kubernetes
  * Tutti gli utenti vengono informati pubblicamente della nuova versione e della versione che verrà rimossa.
  * Quando viene rilasciata una nuova versione della patch, la versione più recente della patch viene rimossa nello stesso momento.
  * I clienti hanno **60 giorni** dalla data di notifica pubblica per eseguire l'aggiornamento a una versione secondaria supportata.
* Per le nuove versioni di **patch** di Kubernetes
  * A tutti gli utenti viene notificata la nuova versione della patch rilasciata e per eseguire l'aggiornamento alla versione più recente della patch.
  * Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione più recente supportata della patch. Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione di patch supportata prima che venga rimosso il meno recente.

AKS definisce "rilasciato" come disponibilità generale, abilitata in tutte le misurazioni SLO/Quality of Service e disponibile in tutte le aree geografiche.

> [!NOTE]
> I clienti ricevono notifiche relative a versioni e deprecazioni di Kubernetes, quando una versione secondaria è deprecata/rimossa. gli utenti vengono assegnati 60 giorni per l'aggiornamento a una versione supportata. Nel caso di versioni di patch, ai clienti vengono assegnati 30 giorni per eseguire l'aggiornamento a una versione supportata.

Le notifiche vengono inviate tramite:

* [Note sulla versione di AKS](https://aka.ms/aks/releasenotes)
* Notifiche portale di Azure
* [Canale di aggiornamento di Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Eccezioni dei criteri

AKS si riserva il diritto di aggiungere o rimuovere le versioni nuove/esistenti che sono state identificate in modo da avere uno o più errori di produzione critici o problemi di sicurezza senza preavviso.

È possibile che vengano ignorate le versioni specifiche della patch o che l'implementazione sia stata accelerata a seconda della gravità del bug o del problema di sicurezza.

### <a name="azure-portal-and-cli-default-versions"></a>portale di Azure e le versioni predefinite dell'interfaccia della riga di comando

Quando si distribuisce un cluster del servizio contenitore di Azure nel portale o con l'interfaccia della riga di comando di Azure, il cluster è sempre impostato sulla versione secondaria N-1 e sulla patch più recente. Ad esempio, se AKS supporta *1.13. x*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11. b*, *1.10. a* + *1.10 b*, la versione predefinita per i nuovi cluster è *1.12. b* .

Il valore predefinito di AKS è N-1 (minor. latestPatch, ad esempio 1.12. b) per fornire ai clienti una versione nota, stabile e con patch per impostazione predefinita.

## <a name="list-currently-supported-versions"></a>Elencare le versioni attualmente supportate

Per informazioni sulle versioni attualmente disponibili per la sottoscrizione e l'area geografica, usare il comando [AZ AKS get-versions][az-aks-get-versions] . L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

L'output è simile all'esempio seguente, che mostra che Kubernetes versione *1.13.5* è la versione più recente disponibile:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.5               None available
1.13.9               1.14.5
1.12.8               1.13.9
1.12.7               1.12.8, 1.13.9
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>Domande frequenti

**Cosa accade quando un cliente esegue l'aggiornamento di un cluster Kubernetes con una versione secondaria che non è supportata?**

Se si utilizza la versione *n-4* , l'utente non è supportato e verrà richiesto di effettuare l'aggiornamento. Se l'aggiornamento dalla versione n-4 a n-3 ha esito positivo, si è ora all'interno dei criteri di supporto. Ad esempio:

- Se le versioni AKS supportate sono *1.13. x*, *1.12. a* + *1.12. b*, *1.11. c* + *1.11 d*e *1.10. e* + *1.10 f* e l'utente si trova in *1,9. g* o *1.9. h*, l'utente non è supportato.
- Se l'aggiornamento da *1,9. g* o *1.9. h* a *1.10. e* o *1.10. f* ha esito positivo, si torna all'interno dei criteri di supporto.

Gli aggiornamenti alle versioni precedenti a *n-4* non sono supportati. In questi casi, è consigliabile creare nuovi cluster del servizio Azure Kubernetes e ridistribuire i carichi di lavoro.

**Cosa significa ' fuori supporto '**

"All'esterno del supporto" significa che la versione in esecuzione è esterna all'elenco delle versioni supportate e verrà richiesto di aggiornare il cluster a una versione supportata quando si richiede il supporto. Inoltre, AKS non esegue alcun runtime o altre garanzie per i cluster al di fuori dell'elenco delle versioni supportate.

**Cosa accade quando un cliente ridimensiona un cluster Kubernetes con una versione secondaria che non è supportata?**

Per le versioni secondarie non supportate dal servizio Azure Kubernetes, il ridimensionamento o la scalabilità orizzontale continuano a funzionare senza problemi.

**Un cliente può usare permanentemente una versione di Kubernetes?**

Sì. Tuttavia, se il cluster non si trova in una delle versioni supportate da AKS, il cluster non è in linea con i criteri di supporto di AKS. Azure non aggiorna o elimina automaticamente il cluster.

**Quale versione effettua il supporto del master se il cluster agente non è presente in una delle versioni supportate del servizio Azure Kubernetes?**

Il master viene aggiornato automaticamente alla versione supportata più recente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [aggiornare un cluster di Azure Kubernetes Service (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
