---
title: Versioni Kubernetes supportate nel servizio Azure Kubernetes
description: Comprendere i criteri di supporto di della versione di Kubernetes e il ciclo di vita dei cluster nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 09/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: a10340d4c2bd2811204af41fba5b32cbe9c4e905
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735067"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Versioni Kubernetes supportate nel servizio Azure Kubernetes

La community di Kubernetes rilascia le versioni secondarie all'incirca ogni tre mesi. Recentemente la community di Kubernetes ha [aumentato la finestra di supporto per ogni versione da 9 a 12 mesi](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), a partire dalla versione 1,19. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni patch sono più frequenti (talvolta settimanali) e sono destinate a correzioni di bug critiche in una versione secondaria. Queste versioni di patch includono correzioni per le vulnerabilità di sicurezza o i bug principali.

## <a name="kubernetes-versions"></a>Versioni di Kubernetes

Kubernetes usa lo schema di controllo delle versioni [semantico](https://semver.org/) standard, il che significa che ogni versione di Kubernetes segue questo schema di numerazione:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* Le versioni principali cambiano quando le modifiche API incompatibili o la compatibilità con le versioni precedenti potrebbero essere interrotte.
* Le versioni secondarie cambiano quando vengono apportate modifiche alle funzionalità che sono compatibili con le altre versioni secondarie.
* Le versioni delle patch cambiano quando vengono apportate correzioni di bug compatibili con le versioni precedenti.

Gli utenti devono eseguire la versione patch più recente della versione secondaria in esecuzione, ad esempio se il cluster di produzione è acceso **`1.17.7`** ed **`1.17.8`** è la versione più recente della patch disponibile per la serie *1,17* , è consigliabile eseguire l'aggiornamento a non appena **`1.17.8`** si è in grado di garantire che il cluster sia completamente aggiornato e supportato.

## <a name="kubernetes-version-support-policy"></a>Criteri di supporto della versione di Kubernetes

AKS definisce una versione disponibile a livello generale, come versione abilitata in tutte le misurazioni SLO o SLA e quando è disponibile in tutte le aree. AKS supporta tre versioni secondarie GA di Kubernetes:

* La versione secondaria di GA più recente rilasciata in AKS (a cui si farà riferimento come N).
* Due versioni secondarie precedenti.
* Ogni versione secondaria supportata supporta anche un massimo di due (2) patch stabili.
* AKS può supportare anche le versioni di anteprima, esplicitamente etichettate e soggette a [termini e condizioni][preview-terms]per l'anteprima.

> [!NOTE]
> AKS USA procedure di distribuzione sicure che coinvolgono una distribuzione graduale dell'area. Ciò significa che possono essere importati fino a 10 giorni lavorativi perché una nuova versione o una nuova versione sia disponibile in tutte le aree.

La finestra supportata di versioni di Kubernetes su AKS è nota come "N-2": (N (ultima versione)-2 (versioni secondarie).

Ad esempio, se AKS introduce *1.17. a* oggi, viene fornito il supporto per le versioni seguenti:

Nuova versione secondaria    |    Elenco versioni supportate
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Dove ". Letter" è rappresentativo delle versioni patch.

Quando viene introdotta una nuova versione secondaria, la versione secondaria più recente e le versioni di patch supportate sono deprecate e rimosse. Se, ad esempio, l'elenco versione supportato corrente è:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

E AKS rilascia 1,18. \* , significa che tutte le versioni 1,15. verranno \* rimosse e non saranno più supportate entro 30 giorni.

> [!NOTE]
> Si noti che se i clienti eseguono una versione di Kubernetes non supportata, verrà richiesto di eseguire l'aggiornamento quando richiede il supporto per il cluster. I cluster che eseguono versioni Kubernetes non supportate non sono coperti dai [criteri di supporto di AKS](./support-policies.md).

Oltre a quanto sopra, AKS supporta un massimo di due versioni di **patch** di una determinata versione secondaria. Quindi, date le seguenti versioni supportate:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Se AKS rilascia `1.17.9` e `1.16.11` , le versioni meno recenti della patch sono deprecate e rimosse e l'elenco delle versioni supportate diventa:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>`kubectl`Versioni supportate

È possibile usare una versione secondaria precedente o successiva `kubectl` rispetto alla versione di *Kube-apiserver* , coerente con i [criteri di supporto di Kubernetes per kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Se, ad esempio, *Kube-apiserver* è in *1,17* , è possibile usare le versioni da *1,16* a *1,18* di `kubectl` con tale *Kube-apiserver* .

Per installare o aggiornare la versione di `kubectl` , eseguire `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Processo di rilascio e di deprecazione

È possibile fare riferimento a versioni imminenti e deprecazioni nel calendario della versione [AKS Kubernetes](#aks-kubernetes-release-calendar).

Per le nuove versioni **secondarie** di Kubernetes
1. AKS pubblica un annuncio preliminare con la data pianificata di una nuova versione della versione e la relativa deprecazione obsoleta nelle note sulla versione di [AKS](https://aka.ms/aks/releasenotes) almeno 30 giorni prima della rimozione.
2. AKS pubblica una [notifica sull'integrità del servizio](../service-health/service-health-overview.md) disponibile per tutti gli utenti con accesso AKS e Portal e invia un messaggio di posta elettronica agli amministratori della sottoscrizione con le date di rimozione della versione pianificata.
3. Gli utenti hanno **30 giorni** dalla rimozione della versione per eseguire l'aggiornamento a una versione secondaria supportata per continuare a ricevere supporto.

Per le nuove versioni di **patch** di Kubernetes
  * A causa della natura urgente delle versioni delle patch, queste possono essere introdotte nel servizio non appena diventano disponibili.
  * In generale, AKS non esegue comunicazioni ampie per il rilascio di nuove versioni di patch. Tuttavia, AKS monitora costantemente e convalida le patch CVE disponibili per il supporto in AKS in modo tempestivo. Se viene rilevata una patch critica o è richiesta un'azione da parte dell'utente, AKS invierà una notifica agli utenti per eseguire l'aggiornamento alla patch appena disponibile.
  * Gli utenti hanno **30 giorni** dal momento in cui una versione patch viene rimossa da AKS per eseguire l'aggiornamento a una patch supportata e continuare a ricevere supporto.

### <a name="supported-versions-policy-exceptions"></a>Eccezioni dei criteri delle versioni supportate

AKS si riserva il diritto di aggiungere o rimuovere le versioni nuove/esistenti che sono state identificate in modo da avere uno o più errori di produzione critici o problemi di sicurezza senza preavviso.

È possibile che vengano ignorate le versioni specifiche della patch o che l'implementazione sia stata accelerata a seconda della gravità del bug o del problema di sicurezza.

## <a name="azure-portal-and-cli-versions"></a>Versioni di portale di Azure e CLI

Quando si distribuisce un cluster del servizio contenitore di Azure nel portale o con l'interfaccia della riga di comando di Azure, per impostazione predefinita viene impostato il valore di N-1 versione secondaria e la patch più recente. Ad esempio, se AKS supporta *1.17. a* , *1.17. b* , *1.16. c* , *1.16. d* , *1.15. e* e *1.15. f* , la versione predefinita selezionata è *1.16. c* .

Per scoprire quali versioni sono attualmente disponibili per la sottoscrizione e l'area in uso, usare il comando [az servizio Azure Kubernetes get-versions][az-aks-get-versions]. L'esempio seguente elenca le versioni di Kubernetes disponibili per l'area *EastUS* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Calendario della versione di AKS Kubernetes

Per la cronologia delle versioni precedenti, vedere [qui](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Versione di K8s | Versione upstream  | Anteprima AKS  | AKS GA  | Fine vita |
|--------------|-------------------|--------------|---------|-------------|
| 1.16  | Sep-19-19  | Gennaio 2019   | Marzo 2020  | Jan 2021 * | 
| 1.17  | 09-19 dicembre  | Gennaio 2019   | 2020 luglio  | 1,20 GA | 
| 1.18  | Mar-23-20  | Maggio 2020   | 2020 agosto  | 1,21 GA | 
| 1,19  | Aug-04-20  | 2020 Sep   | 2020 novembre  | 1,22 GA | 
| 1,20  | 08-20 dicembre  | Gennaio 2021   | Mar 2021  | 1,23 GA | 
\* A causa della stagione di festività AKS, il ciclo di vita del 1,16 verrà esteso dal 2020 novembre fino al 2021 gennaio. Per altre informazioni, leggere [qui](https://github.com/Azure/AKS/releases/tag/2020-10-12).

## <a name="faq"></a>Domande frequenti

**Con quale frequenza si prevede di aggiornare le versioni di Kubernetes per continuare a supportare?**

Con la dichiarazione di Kubernetes 1,19, la [community open source ha esteso il supporto per 1 anno](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS si impegna ad abilitare le patch e a supportare la corrispondenza minima degli impegni upstream. Ciò significa che, a partire da cluster AKS in 1,19, sarà possibile eseguire l'aggiornamento almeno una volta all'anno per rimanere in una versione supportata. Per le versioni 1,18 o inferiori, la finestra del supporto rimane a 9 mesi, che richiede un aggiornamento ogni 9 mesi per restare in una versione supportata. È consigliabile testare regolarmente le nuove versioni e prepararsi all'aggiornamento alle versioni più recenti per acquisire i miglioramenti più recenti stabili all'interno di Kubernetes.

**Cosa accade quando un utente aggiorna un cluster Kubernetes con una versione secondaria non supportata?**

Se si usa la versione *n-3* o precedente, significa che l'utente non è più supportato e verrà richiesto di effettuare l'aggiornamento. Quando l'aggiornamento dalla versione n-3 a n-2 ha esito positivo, si torna all'interno dei criteri di supporto. Ad esempio:

- Se la versione più recente di AKS supportata è *1.15. a* e l'utente si trova su *1.14. b* o versioni precedenti, l'utente non è più supportato.
- Quando l'aggiornamento da *1.14. b* a *1.15. a* o versione successiva ha esito positivo, si torna all'interno dei criteri di supporto.

Il downgrade non è supportato.

**Cosa significa ' esterno al supporto tecnico '**

"All'esterno del supporto" significa che la versione in esecuzione è esterna all'elenco delle versioni supportate e verrà richiesto di aggiornare il cluster a una versione supportata quando si richiede il supporto, a meno che non si sia entro il periodo di tolleranza di 30 giorni dopo la deprecazione della versione. Inoltre, AKS non esegue alcun runtime o altre garanzie per i cluster al di fuori dell'elenco delle versioni supportate.

**Cosa accade quando un utente ridimensiona un cluster Kubernetes con una versione secondaria non supportata?**

Per le versioni secondarie non supportate da AKS, la scalabilità orizzontale o verticale dovrebbe continuare a funzionare, ma non vi sono garanzie di qualità del servizio, pertanto è consigliabile eseguire l'aggiornamento per ripristinare il supporto del cluster.

**Un utente può rimanere sempre in una versione di Kubernetes?**

Se un cluster non è più supportato per più di tre (3) versioni secondarie ed è stato trovato per portare a rischi per la sicurezza, Azure Contatta l'utente per aggiornare in modo proattivo il cluster. Se non si eseguono altre azioni, Azure si riserva il diritto di aggiornare automaticamente il cluster per conto dell'utente.

**Quale versione supporta il piano di controllo se il pool di nodi non si trova in una delle versioni AKS supportate?**

Il piano di controllo deve trovarsi all'interno di una finestra di versioni di tutti i pool di nodi. Per informazioni dettagliate sull'aggiornamento del piano di controllo o dei pool di nodi, vedere la documentazione sull' [aggiornamento dei pool di nodi](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**È possibile ignorare più versioni AKS durante l'aggiornamento del cluster?**

Quando si aggiorna un cluster AKS supportato, non è possibile ignorare le versioni secondarie di Kubernetes. Ad esempio, gli aggiornamenti compresi tra *1.12. x*  ->  *1.13. x* o *1.13. x*  ->  *1.14.* x sono consentiti, tuttavia *1.12* . x  ->  *1.14. x* non lo è.

Per eseguire l'aggiornamento, da *1.12. x*  ->  *1.14. x* , eseguire prima l'aggiornamento da *1.12.* x  ->  *1.13. x* , quindi eseguire l'aggiornamento da *1.13. x*  ->  *1.14. x* .

È possibile ignorare più versioni solo quando si effettua l'aggiornamento da una versione non supportata di nuovo in una versione supportata. Ad esempio, è possibile eseguire l'aggiornamento da un valore *1,10. x* non supportato--> è possibile completare un valore *1.15. x* supportato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come aggiornare il cluster, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
