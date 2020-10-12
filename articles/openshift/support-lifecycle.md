---
title: Ciclo di vita del supporto di Azure Red Hat OpenShift
description: Informazioni sul ciclo di vita del supporto e sulle versioni supportate per Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: c066931cece60d14767b86254020ea5ba4bad1be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854506"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Ciclo di vita del supporto per Azure Red Hat OpenShift 4

Red Hat rilascia le versioni secondarie di Red Hat OpenShift container Platform (OCP) circa ogni tre mesi. Queste versioni includono miglioramenti e nuove funzionalità. Le versioni patch sono più frequenti (in genere settimanalmente) e sono destinate solo a correzioni di bug critiche in una versione secondaria. Queste versioni di patch possono includere correzioni per le vulnerabilità di sicurezza o i bug principali.

Azure Red Hat OpenShift è stato creato da versioni specifiche di OCP. Questo articolo illustra le versioni di OCP supportate per Azure Red Hat OpenShift e informazioni dettagliate sugli aggiornamenti, le deprecazioni e i criteri di supporto.

## <a name="red-hat-openshift-versions"></a>Versioni di Red Hat OpenShift

Red Hat OpenShift container Platform usa il controllo delle versioni semantico. Il controllo delle versioni semantiche utilizza diversi livelli di numeri di versione per specificare diversi livelli di controllo delle versioni. La tabella seguente illustra le diverse parti di un numero di versione semantico, in questo caso usando il numero di versione di esempio 4.4.11.

|Versione principale (x)|Versione secondaria (y)|Patch (z)|
|-|-|-|
|4|4|11|

Ogni numero nella versione indica la compatibilità generale con la versione precedente:

* **Versione principale**: al momento non sono pianificate versioni principali. Le versioni principali cambiano quando le modifiche API incompatibili o la compatibilità con le versioni precedenti potrebbero essere interrotte.
* **Versione secondaria**: rilasciata approssimativamente ogni tre mesi. Gli aggiornamenti delle versioni secondarie possono includere funzionalità aggiuntive, miglioramenti, deprecazioni, rimozioni, correzioni di bug, miglioramenti della sicurezza e altri miglioramenti.
* **Patch**: in genere rilasciate ogni settimana o in base alle esigenze. Gli aggiornamenti della versione patch possono includere correzioni di bug, miglioramenti della sicurezza e altri miglioramenti.

I clienti devono puntare a eseguire la versione secondaria più recente della versione principale che sta eseguendo. Se, ad esempio, il cluster di produzione è 4,4 e 4,5 è la versione secondaria disponibile a livello generale più recente per la serie 4, è consigliabile eseguire l'aggiornamento a 4,5 non appena possibile.

### <a name="upgrade-channels"></a>Aggiornare i canali

I canali di aggiornamento sono collegati a una versione secondaria di Red Hat OpenShift container Platform (OCP). Ad esempio, i canali di aggiornamento OCP 4,4 non includeranno mai un aggiornamento a una versione 4,5. I canali di aggiornamento controllano solo la selezione della versione e non influiscano sulla versione del cluster.

Azure Red Hat OpenShift 4 supporta solo canali stabili. Ad esempio: Stable-4,4.

È possibile usare il canale stabile 4,5 per eseguire l'aggiornamento da una versione secondaria precedente di Azure Red Hat OpenShift. I cluster aggiornati con i canali Fast, prerelease e candidate non saranno supportati.

Se si passa a un canale che non include la versione corrente, viene visualizzato un avviso e non è possibile eseguire alcuna operazione di aggiornamento, ma è possibile modificare in modo sicuro il canale originale in qualsiasi momento.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Criteri di supporto della versione di Red Hat OpenShift container Platform

Azure Red Hat OpenShift supporta due versioni secondarie disponibili a livello generale (GA) di Red Hat OpenShift container Platform:
* La versione secondaria di GA più recente rilasciata in Azure Red Hat OpenShift (a cui si fa riferimento come N)
* Una versione secondaria precedente (N-1)

Se disponibile in un canale di aggiornamento stabile, potrebbero essere supportate anche le versioni secondarie più recenti (N + 1, N + 2) disponibili in upstream OCP.

Gli aggiornamenti critici delle patch vengono applicati automaticamente ai cluster da Azure Red Hat OpenShift site Affiy Engineers (SRE). I clienti che desiderano installare gli aggiornamenti delle patch in anticipo sono gratuiti.

Ad esempio, se Azure Red Hat OpenShift introduce attualmente 4.5. z, viene fornito il supporto per le versioni seguenti:

|Nuova versione secondaria|Elenco versioni supportate|
|-|-|
|4.5. z|4.5. z, 4.4. z|

". z" rappresenta le versioni patch. Se disponibile in un canale di aggiornamento stabile, i clienti possono anche eseguire l'aggiornamento a 4.6. z.

Quando viene introdotta una nuova versione secondaria, la versione secondaria meno recente viene deprecata e rimossa. Si immagini, ad esempio, che l'elenco versione supportato corrente sia 4.5. z e 4.4. z. Quando Azure Red Hat OpenShift rilascia 4.6. z, la versione 4.4. z verrà rimossa e non sarà più supportata entro 30 giorni.

> [!NOTE]
> Si noti che se i clienti eseguono una versione non supportata di Red Hat OpenShift, è possibile che venga richiesto di eseguire l'aggiornamento quando viene richiesto il supporto per il cluster. I cluster che eseguono versioni non supportate di Red Hat OpenShift non sono coperti dal contratto di servizio di Azure Red Hat OpenShift.

## <a name="release-and-deprecation-process"></a>Processo di rilascio e di deprecazione

È possibile fare riferimento a versioni imminenti e deprecazioni nel calendario della versione di Azure Red Hat OpenShift.

Per le nuove versioni secondarie di Red Hat OpenShift container Platform:
* Il team di Azure Red Hat OpenShift SRE pubblica un annuncio preliminare con la data pianificata di una nuova versione e la rispettiva obsoleta versione precedente nelle note sulla versione di [Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) almeno 30 giorni prima della rimozione.
* Il team di Azure Red Hat OpenShift SRE pubblica una notifica sull'integrità del servizio disponibile per tutti i clienti con Azure Red Hat OpenShift e l'accesso al portale e invia un messaggio di posta elettronica agli amministratori della sottoscrizione con le date di rimozione della versione pianificata.
* I clienti hanno 30 giorni dalla rimozione della versione per eseguire l'aggiornamento a una versione secondaria supportata per continuare a ricevere supporto.

Per le nuove versioni di patch di Red Hat OpenShift container Platform:
* A causa della natura urgente delle versioni delle patch, queste possono essere introdotte nel servizio dal team di Azure Red Hat OpenShift SRE non appena diventano disponibili.
* In generale, il team di Azure Red Hat OpenShift SRE non esegue comunicazioni di ampia portata per l'installazione di nuove versioni di patch. Tuttavia, il team monitora e convalida costantemente le patch CVE disponibili per supportarle in modo tempestivo. Se è richiesta un'azione da parte del cliente, il team invierà una notifica ai clienti sull'aggiornamento.

## <a name="supported-versions-policy-exceptions"></a>Eccezioni dei criteri delle versioni supportate

Il team di Azure Red Hat OpenShift SRE si riserva il diritto di aggiungere o rimuovere versioni nuove/esistenti o ritardare le versioni di rilascio secondarie imminenti, che sono state identificate in modo da avere uno o più problemi di produzione critici che incidono sui bug o sui problemi di sicurezza senza preavviso.

È possibile che vengano ignorate versioni specifiche della patch oppure che l'implementazione venga accelerata a seconda della gravità del problema o del problema di sicurezza.

## <a name="azure-portal-and-cli-versions"></a>Versioni di portale di Azure e CLI

Quando si distribuisce un cluster Azure Red Hat OpenShift nel portale o con l'interfaccia della riga di comando di Azure, il cluster viene impostato per impostazione predefinita sulla versione più recente (N) e sulla patch critica più recente. Se ad esempio Azure Red Hat OpenShift supporta 4.5. z e 4.4. z, la versione predefinita per le nuove installazioni è 4.5. z. I clienti che desiderano usare la versione più recente di aggiornamento della versione secondaria a Monte OCP (N + 1, N + 2) possono aggiornare il cluster in qualsiasi momento a qualsiasi versione disponibile nei canali di aggiornamento stabili.

## <a name="azure-red-hat-openshift-release-calendar"></a>Calendario della versione di Azure Red Hat OpenShift

Vedere la guida seguente per la [cronologia delle versioni precedenti di Red Hat OpenShift container Platform (upstream)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|Versione OCP|Versione upstream|Disponibilità generale di Azure Red Hat OpenShift|Fine vita|
|-|-|-|-|
|4.3|Febbraio 2020|Maggio 2020|Agosto 2020|
|4.4|Maggio 2020|Agosto 2020|4.6 GA|
|4.5|Luglio 2020|2020 ottobre|4,7 GA
|4.6|* Inizio trimestre, 2020|* Tardo Q4, 2020|4,8 GA|

\*_Conferma della data di rilascio upstream in sospeso._

## <a name="faq"></a>Domande frequenti

**Cosa accade quando un utente aggiorna un cluster OpenShift con una versione secondaria non supportata?**

Se si utilizza la versione N-2 o precedente, significa che l'utente non è supportato e verrà richiesto di effettuare l'aggiornamento. Quando l'aggiornamento dalla versione N-2 a N-1 ha esito positivo, si torna all'interno dei criteri di supporto. Ad esempio:
* Se la versione più recente supportata di Azure Red Hat OpenShift è 4.4. z e l'utente si trova in 4.3. z o versioni precedenti, l'utente non è supportato.
* Quando l'aggiornamento da 4.3. z a 4.4. z o versione successiva ha esito positivo, si torna all'interno dei criteri di supporto.

Il ripristino di una versione precedente del cluster o di un rollback non è supportato. È supportato solo l'aggiornamento a una versione più recente.

**Cosa significa "all'esterno del supporto"?**

"All'esterno del supporto" significa che la versione in esecuzione è esterna all'elenco delle versioni supportate e potrebbe essere necessario aggiornare il cluster a una versione supportata quando viene richiesto il supporto, a meno che non si rientri nel periodo di tolleranza di 30 giorni dopo la deprecazione della versione. Inoltre, Azure Red Hat OpenShift non esegue alcuna garanzia di runtime o SLA per i cluster al di fuori dell'elenco delle versioni supportate alla fine del periodo di tolleranza di 30 giorni.
