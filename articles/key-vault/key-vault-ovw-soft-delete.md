---
ms.assetid: 
title: Eliminazione temporanea di Azure Key Vault | Microsoft Docs
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2b73fb55384cdcc8db2736557e0efef97b34fc45
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---
<a id="azure-key-vault-soft-delete-feature-overview" class="xliff"></a>

# Panoramica della funzionalità di eliminazione temporanea di Azure Key Vault

>[!NOTE]
>Per questa versione di anteprima di Azure Key Vault è disponibile in anteprima solo la funzionalità di **eliminazione temporanea**. Azure Key Vault è, nel suo complesso, un servizio di produzione completo.

Per questa versione di anteprima di Azure Key Vault viene descritta l'eliminazione reversibile di insiemi di credenziali delle chiavi e di oggetti di insiemi di credenziali delle chiavi, nota come eliminazione temporanea. In particolare, vengono affrontati gli scenari seguenti:

- Supporto per l'eliminazione reversibile di insiemi di credenziali delle chiavi
- Supporto per l'eliminazione reversibile di oggetti di insiemi di credenziali delle chiavi (ad esempio, chiavi, segreti e certificati)

<a id="supporting-interfaces" class="xliff"></a>

## Supporto di interfacce

La funzionalità di eliminazione temporanea è inizialmente disponibile attraverso le interfacce REST, .NET/C# e PowerShell. Per informazioni dettagliate, vedere [Documentazione sull'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/).

<a id="scenarios" class="xliff"></a>

## Scenari

Gli insiemi di credenziali delle chiavi di Azure sono risorse tracciate, gestite da Azure Resource Manager. Azure Resource Manager specifica anche un comportamento ben definito per l'eliminazione, in base al quale al termine dell'operazione di eliminazione la risorsa non deve essere più accessibile. Con la funzionalità di eliminazione temporanea viene consentito invece il ripristino dell'oggetto eliminato, indipendentemente dal fatto che l'eliminazione sia stata intenzionale o accidentale.

1. In uno scenario tipico, un utente può aver accidentalmente eliminato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme è stato impostato in modo da essere recuperabile entro un periodo predeterminato, l'utente può annullare l'eliminazione e ripristinare i dati.

2. In uno scenario diverso, un utente non autorizzato può tentare di eliminare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi, ad esempio una chiave in un insieme di credenziali, per provocare un'interruzione delle attività. In questo caso, separare l'eliminazione dell'insieme di credenziali delle chiavi o dell'oggetto dell'insieme di credenziali delle chiavi dall'eliminazione effettiva dei dati sottostanti può rappresentare una misura di sicurezza, poiché consente, ad esempio, di limitare le autorizzazioni di eliminazione dei dati solo a un ruolo attendibile. In questo modo, infatti, è necessario un quorum per un'operazione che, altrimenti, determinerebbe una perdita immediata di dati.

<a id="soft-delete-behavior" class="xliff"></a>

### Comportamento della funzione di eliminazione temporanea

Con questa versione di anteprima, l'operazione di eliminazione eseguita su un insieme di credenziali delle chiavi o su un oggetto di un insieme di credenziali delle chiavi è di fatto un'operazione di eliminazione temporanea, poiché in realtà le risorse vengono mantenute per un periodo di memorizzazione specifico, seppur risultino apparentemente eliminate. Il servizio offre anche un meccanismo per il ripristino dell'oggetto eliminato, essenzialmente annullando l'operazione di eliminazione. 

L'eliminazione temporanea è un comportamento facoltativo di Key Vault e in questa versione **non è abilitato per impostazione predefinita**. Per informazioni dettagliate sull'abilitazione dell'eliminazione temporanea per un insieme di credenziali delle chiavi, vedere le istruzioni corrispondenti negli argomenti di riferimento per l'interfaccia in uso in [Documentazione sull'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/).

<a id="key-vault-recovery" class="xliff"></a>

### Recupero di un insieme di credenziali delle chiavi

In seguito all'eliminazione di un insieme di credenziali delle chiavi, il servizio creerà una risorsa proxy nella sottoscrizione, a cui aggiungerà metadati sufficienti per il ripristino. La risorsa proxy è un oggetto archiviato disponibile nello stesso percorso dell'insieme di credenziali delle chiavi eliminato. 

<a id="key-vault-object-recovery" class="xliff"></a>

### Recupero di un oggetto di un insieme di credenziali delle chiavi

In seguito all'eliminazione di un oggetto di un insieme di credenziali delle chiavi, ad esempio una chiave, il servizio imposterà l'oggetto su uno stato eliminato, rendendolo inaccessibile per qualsiasi operazione di recupero. In questo stato, l'oggetto dell'insieme di credenziali delle chiavi può essere solo elencato, recuperato o eliminato in modo forzato o permanente. 

Al tempo stesso, Key Vault pianificherà l'esecuzione dell'eliminazione dei dati sottostanti, corrispondenti all'insieme di credenziali delle chiavi o all'oggetto dell'insieme di credenziali delle chiavi eliminato, dopo un intervallo di memorizzazione predeterminato. Per la durata dell'intervallo di memorizzazione viene conservato anche il record DNS corrispondente all'insieme di credenziali delle chiavi.

<a id="soft-delete-retention-period" class="xliff"></a>

### Periodo di memorizzazione dell'eliminazione temporanea

Le risorse eliminate in modo temporaneo vengono conservate per un periodo di tempo prestabilito di 90 giorni. 

>[!NOTE]
> Questo periodo di memorizzazione dell'eliminazione temporanea non è configurabile nella versione di anteprima rilasciata il 10 maggio 2017. 

Durante il periodo di memorizzazione dell'eliminazione temporanea tenere presente quanto segue:

- È possibile elencare tutti gli insiemi di credenziali delle chiavi e gli oggetti di insiemi di credenziali delle chiavi in stato di eliminazione temporanea nella sottoscrizione, nonché accedere alle informazioni di eliminazione e recupero disponibili su di essi.
    - Gli insiemi di credenziali delle chiavi eliminati possono essere elencati solo da utenti con autorizzazioni speciali. Per la gestione degli insiemi di credenziali delle chiavi eliminati è consigliabile creare un ruolo personalizzato a cui assegnare queste autorizzazioni speciali.
- Non è possibile creare nello stesso percorso due insiemi di credenziali delle chiavi con lo stesso nome. Analogamente, non è possibile creare un insieme di credenziali delle chiavi in un determinato insieme di credenziali se l'insieme di credenziali delle chiavi contiene un oggetto con lo stesso nome impostato in uno stato eliminato. 
- Solo un utente con privilegi specifici può recuperare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di ripristino sulla risorsa proxy corrispondente.
    - Può recuperare l'insieme di credenziali delle chiavi l'utente che, membro del ruolo personalizzato, dispone dei privilegi necessari per creare un insieme di credenziali delle chiavi nel gruppo di risorse.
- Solo un utente con privilegi specifici può eliminare in modo forzato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di eliminazione sulla risorsa proxy corrispondente.

Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme di credenziali delle chiavi non viene recuperato, alla fine dell'intervallo di memorizzazione il servizio esegue un'operazione di ripulitura sull'insieme di credenziali delle chiavi o sull'oggetto dell'insieme eliminato in modo temporaneo, nonché sui relativi contenuti. È possibile che l'eliminazione della risorsa non venga ripianificata.

<a id="permissioned-purge" class="xliff"></a>

### Operazione di ripulitura con autorizzazione

L'eliminazione permanente di un insieme di credenziali delle chiavi non è possibile tramite un'operazione POST sulla risorsa proxy e richiede privilegi speciali. In genere, solo il proprietario della sottoscrizione può eliminare in modo permanente un insieme di credenziali delle chiavi. L'operazione POST attiva l'eliminazione immediata e irreversibile dell'insieme di credenziali delle chiavi. 

Unica eccezione a questa conseguenza è che la sottoscrizione di Azure sia stata contrassegnata come *non cancellabile*. In questo caso, solo il servizio può eseguire l'eliminazione effettiva, sotto forma di processo pianificato. 




