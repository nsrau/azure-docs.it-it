---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 2d555908007f4e43a38b6d0eff909ef5050878ea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069665"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni delle patch sono più frequenti (in alcuni casi settimanali) e sono destinate esclusivamente a correzioni di bug importanti in una versione secondaria. Le versioni delle patch consentono di risolvere le vulnerabilità della sicurezza o i bug rilevanti con impatto su un numero elevato di clienti e prodotti in esecuzione in ambienti di produzione basati su Kubernetes.

Servizio contenitore di AZURE è possibile certificare e rilasciare nuove versioni di Kubernetes entro 30 giorni di un rilascio a monte, soggette alla stabilità della versione.

## <a name="kubernetes-versions"></a>Versioni di Kubernetes

Kubernetes Usa lo standard [Versionamento semantico](https://semver.org/) regime di controllo delle versioni. Ciò significa che ogni versione di Kubernetes segue questo schema di numerazione:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
  1.13.7
```

Ogni numero di versione indica la compatibilità generale con la versione precedente:

* Cambiamenti delle API di versioni principali modifiche quando compatibile o con le versioni precedenti compatibilità può essere interrotta.
* Le versioni secondarie cambiano quando vengono apportate modifiche di funzionalità che sono compatibili con le versioni precedenti per le altre versioni secondarie.
* Vengono apportate modifiche le versioni patch quando correzioni di bug di compatibilità.

In generale, gli utenti è necessario cercare la versione patch più recente della versione secondaria vengono eseguiti, ad esempio se il cluster di produzione è in esecuzione *1.13.6* e *1.13.7* è la patch più recente disponibile versione disponibile per il *1.13* serie, è necessario aggiornare a *1.13.7* , non appena si è in grado di verificare che il cluster è completamente con patch e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

Il servizio Azure Kubernetes supporta quattro versioni secondarie di Kubernetes:

* La versione secondaria corrente che viene rilasciata nel servizio contenitore di AZURE (N)
* Tre versioni secondarie precedenti. Ogni versione secondaria supportata supporta anche due patch stabili.

Questo è noto come "N-3"-(N (versione più recente) - 3 (le versioni secondarie)).

Se, ad esempio, servizio contenitore di AZURE introduce *1.13.x* oggi, viene fornito supporto per le versioni seguenti:

Nuovo elenco delle versioni supportate di versione secondaria
-----------------        ----------------------
1.13.x                   1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b

Dove "x" e "1).a" e "b" sono le versioni patch rappresentativo.

Per informazioni dettagliate sulle comunicazioni relative alle modifiche della versione e le aspettative, vedere "Le comunicazioni" riportata di seguito.

Quando è stata introdotta una nuova versione secondaria, i meno recenti secondaria patch e versione le versioni supportate sono deprecate e rimosso. Ad esempio, se l'oggetto corrente è supportata elenco delle versioni sarà:

<a name="supported-version-list"></a>Elenco delle versioni supportate
----------------------
1.12.a, 1.12.b, 1.11.a, 1.11.b, 1.10.a, 1.10.b, 1.9.a, 1.9.b

E servizio contenitore di AZURE rilascia 1.13.x, ciò significa che le versioni 1.9.x (tutte le 1,9 versioni) verranno rimossa e all'esterno di supporto.

> [!NOTE]
> Si noti che se i clienti eseguono una versione di Kubernetes non supportata, gli verrà richiesto di aggiornare la richiesta di supporto per il cluster. I cluster che eseguono versioni di Kubernetes non supportate non vengono analizzati i [servizio contenitore di AZURE supportano i criteri](https://docs.microsoft.com/azure/aks/support-policies).


Oltre alle precedenti versioni secondarie, servizio contenitore di AZURE supporta le due più recente *patch** nelle versioni di una determinata versione secondaria. Dato ad esempio, le versioni supportate seguenti:

<a name="supported-version-list"></a>Elenco delle versioni supportate
----------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5

Se Kubernetes upstream rilasciata 1.12.3 e 1.11.6 e servizio contenitore di AZURE rilascia quelle versioni di patch, le versioni di patch meno recenti sono deprecate e rimosse e diventa l'elenco delle versioni supportate:

<a name="supported-version-list"></a>Elenco delle versioni supportate
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*

### <a name="communications"></a>Comunicazioni

* Per ottenere nuove **secondaria** le versioni di Kubernetes
  * Tutti gli utenti ricevono una notifica della nuova versione e quale versione verrà rimosso.
  * I clienti che eseguono la versione **deve essere rimossa** riceverà una notifica che abbiano **60 giorni** eseguire l'aggiornamento a una versione supportata (ad esempio, versione secondaria).
* Per ottenere nuove **patch** le versioni di Kubernetes
  * Tutti gli utenti ricevono una notifica della nuova versione patch rilasciati e per l'aggiornamento alla versione di patch più recente.
  * Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione patch più recenti e supportati. Gli utenti hanno **30 giorni** per eseguire l'aggiornamento a una versione patch supportate prima che venga rimosso dal meno recente.

Servizio contenitore di AZURE definisce "lanciati" disponibilità generale, abilitato in tutti i SLO / qualità delle misurazioni di servizio e disponibile in tutte le aree.

> [!NOTE]
> I clienti ricevono una notifica di Kubernetes versione rilascia e deprecati, quando una versione secondaria è deprecato o rimosso gli utenti vengono concesse 60 giorni per eseguire l'aggiornamento a una versione supportata. Nel caso di versioni di patch, i clienti ha 30 giorni per l'aggiornamento a una versione supportata.

Le notifiche vengono inviate tramite:

* [Note sulla versione servizio contenitore di AZURE](https://aka.ms/aks/releasenotes)
* Portale di Azure le notifiche
* [Canale di aggiornamento di Azure][azure-update-channel]

### <a name="policy-exceptions"></a>Eccezioni dei criteri

Servizio contenitore di AZURE si riserva il diritto di aggiungere o rimuovere le versioni nuove o esistenti che sono state identificate per avere una o più produzione critici conseguenze per i bug o problemi di sicurezza senza preavviso.

Le versioni di patch specifici possono essere ignorate o l'implementazione con accelerazione in base alla gravità del problema bug o della sicurezza.

### <a name="azure-portal-and-cli-default-versions"></a>Portale di Azure e le versioni predefinite dell'interfaccia della riga

Quando si distribuisce un cluster del servizio contenitore di AZURE nel portale o con la CLI di Azure, il cluster è sempre impostato per la versione secondaria di n-1 e la patch più recente. Se, ad esempio, servizio contenitore di AZURE supporta *1.13.x*, *1.12.a* + *1.12.b*, *1.11.a*  +   *1.11.b*, *1.10.a* + *1.10b*, la versione predefinita per i nuovi cluster è *1.12.b*.

Servizio contenitore di AZURE per impostazione predefinita per n-1 (minor.latestPatch, ad esempio 1.12.b) per offrire ai clienti un gruppo noto, stabile e una versione con patch per impostazione predefinita.

## <a name="list-currently-supported-versions"></a>Elencare le versioni attualmente supportate

Per scoprire quali versioni sono attualmente disponibili per la sottoscrizione e l'area in uso, usare il comando [az servizio Azure Kubernetes get-versions][az-aks-get-versions]. L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS*:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

L'output è simile all'esempio seguente, che mostra che la versione Kubernetes *1.13.5* è disponibile la versione più recente:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Domande frequenti

**Cosa accade quando un cliente esegue l'aggiornamento di un cluster Kubernetes con una versione secondaria che non è supportata?**

Se si usa la *n-4* versione, sono di fuori di supporto e verrà richiesto di eseguire l'aggiornamento. Se l'aggiornamento dalla versione n-4 a n-3 ha esito positivo, sono ora entro i criteri di supporto. Ad esempio:

- Se sono le versioni supportate di AKS *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c*  +  *1!d 1.11*, e *1.10.e* + *1.10F* e si usa *1.9.g* oppure *1.9.h*, si è di fuori di supporto.
- Se l'aggiornamento dal *1.9.g* oppure *1.9.h* a *1.10.e* oppure *1.10.f* ha esito positivo, si è nuovamente nell'entro i criteri di supporto.

Gli aggiornamenti alle versioni precedenti a *n-4* non sono supportati. In questi casi, è consigliabile creare nuovi cluster del servizio Azure Kubernetes e ridistribuire i carichi di lavoro.

**Che cosa significa "di supporto"**

'All'esterno di supporto' significa che la versione che è in esecuzione non è compreso l'elenco di versioni supportate e verrà richiesto per aggiornare il cluster a una versione supportata quando si richiede supporto. Inoltre, AKS non esegue qualsiasi runtime o altri garanzie per i cluster esterno dell'elenco di versioni supportate.

**Cosa accade quando un cliente ridimensiona un cluster Kubernetes con una versione secondaria che non è supportata?**

Per le versioni secondarie non supportate dal servizio Azure Kubernetes, il ridimensionamento o la scalabilità orizzontale continuano a funzionare senza problemi.

**Un cliente può usare permanentemente una versione di Kubernetes?**

Sì. Tuttavia, se il cluster non è in una delle versioni supportate dal servizio contenitore di AZURE, il cluster è i criteri di supporto del servizio contenitore di AZURE. Azure non aggiorna o elimina automaticamente il cluster.

**Quale versione effettua il supporto del master se il cluster agente non è presente in una delle versioni supportate del servizio Azure Kubernetes?**

Il master viene aggiornato automaticamente alla versione supportata più recente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
