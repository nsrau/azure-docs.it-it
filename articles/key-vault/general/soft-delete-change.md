---
title: L'eliminazione temporanea verrà abilitata in tutte le istanze di Azure Key Vault | Microsoft Docs
description: Usare questo documento per adottare l'eliminazione temporanea per tutti gli insiemi di credenziali delle chiavi.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: 91068bacee0bde9e11cc23816bd8e2a101854fb2
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388282"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>L'eliminazione temporanea verrà abilitata in tutti gli insiemi di credenziali delle chiavi

> [!WARNING]
> **Modifica di rilievo**: la possibilità di rifiutare esplicitamente l'eliminazione temporanea verrà deprecata entro la fine dell'anno e la protezione dell'eliminazione temporanea verrà attivata automaticamente per tutti gli insiemi di credenziali delle chiavi.  Utenti e amministratori di Azure Key Vault dovranno abilitare immediatamente l'eliminazione temporanea sugli insiemi di credenziali delle chiavi.

Senza protezione dell'eliminazione temporanea, un segreto eliminato da un insieme di credenziali delle chiavi viene rimosso definitivamente. Gli utenti possono attualmente rifiutare esplicitamente l'eliminazione temporanea durante la creazione dell'insieme di credenziali delle chiavi, ma, per proteggere i segreti da eliminazioni accidentali o dannose da parte di un utente, Microsoft abiliterà la protezione dell'eliminazione temporanea in **tutti** insiemi di credenziali delle chiavi e gli utenti non avranno più la possibilità di rifiutarla o disattivarla.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<alt text>":::

Per informazioni dettagliate sulla funzionalità di eliminazione temporanea, vedere [Panoramica dell'eliminazione temporanea di Azure Key Vault](soft-delete-overview.md).

## <a name="how-do-i-respond-to-breaking-changes"></a>Come rispondere alle modifiche di rilievo

Non è possibile creare un oggetto dell'insieme di credenziali delle chiavi con lo stesso nome di un oggetto dell'insieme di credenziali delle chiavi nello stato di eliminazione temporanea.  Se ad esempio si elimina una chiave denominata `test key` nell'insieme di credenziali delle chiavi A, non sarà possibile creare una nuova chiave denominata `test key` nell'insieme di credenziali delle chiavi A finché l'oggetto `test key` eliminato temporaneamente non viene rimosso.

### <a name="application-changes"></a>Modifiche dell'applicazione

Se l'applicazione presuppone che l'eliminazione temporanea non sia abilitata e prevede che i nomi del segreto o dell'insieme di credenziali delle chiavi eliminato siano disponibili per il riutilizzo immediato, sarà necessario apportare le modifiche seguenti alla logica dell'applicazione per adottare questa modifica.

1. Eliminare l'insieme di credenziali delle chiavi o il segreto originale
2. Rimuovere definitivamente l'insieme di credenziali delle chiavi o il segreto nello stato di eliminazione temporanea.
3. Attenzione: la ricreazione immediata può causare un conflitto.
4. Ricreare l'insieme di credenziali delle chiavi con lo stesso nome.
5. Implementare la ripetizione di tentativi se l'operazione di creazione continua a generare un errore di conflitto di nomi. Nello scenario peggiore, l'aggiornamento dei record DNS può richiedere fino a 10 minuti.

### <a name="administration-changes"></a>Modifiche dell'amministrazione

Alle entità di sicurezza che devono accedere per eliminare definitivamente i segreti è necessario concedere autorizzazioni aggiuntive dei criteri di accesso per rimuovere definitivamente questi segreti e l'insieme di credenziali delle chiavi.

Se gli insieme di credenziali delle chiavi prevedono un criterio di Azure che impone la disattivazione dell'eliminazione temporanea, è necessario disabilitare tale criterio.  Può essere necessario inoltrare questo problema a un amministratore che controlla i criteri di Azure applicati all'ambiente. Se questo criterio non viene disabilitato, è possibile che si perda la possibilità di creare nuovi insiemi di credenziali delle chiavi nell'ambito di applicazione del criterio.

Se l'organizzazione ha o è soggetta a requisiti di conformità legali e non può consentire la conservazione in uno stato recuperabile di insiemi di credenziali delle chiavi e segreti eliminati, per un periodo prolungato di tempo, sarà necessario modificare il periodo di conservazione dell'eliminazione temporanea, configurabile tra 7 e 90 giorni, per soddisfare gli standard dell'organizzazione.

## <a name="procedures"></a>Procedure

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Controllare gli insiemi di credenziali delle chiavi per verificare se l'eliminazione temporanea è abilitata

1. Accedere al portale di Azure.
2. Cercare "Criteri di Azure".
3. Selezionare "Definizioni".
4. In Categoria selezionare "Key Vault" nel filtro.
5. Selezionare il criterio "Gli oggetti Key Vault devono essere recuperabili".
6. Fare clic su "Assegna".
7. Impostare l'ambito della sottoscrizione.
8. Selezionare "Rivedi e crea".
9. Possono essere necessarie fino a 24 ore prima che venga completata un'analisi completa dell'ambiente.
10. Nel pannello Criteri di Azure fare clic su "Conformità".
11. Selezionare il criterio applicato.

A questo punto dovrebbe essere possibile filtrare e verificare per quali insiemi di credenziali delle chiavi è abilitata l'eliminazione temporanea (risorse conformi) e per quali non è abilitata (risorse non conformi).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Attivare l'eliminazione temporanea per un insieme di credenziali delle chiavi esistente

1. Accedere al portale di Azure.
2. Cercare l'insieme di credenziali delle chiavi.
3. Selezionare "Proprietà" nelle impostazioni.
4. In Eliminazione temporanea selezionare il pulsante di opzione corrispondente a "Abilita il ripristino di questo insieme di credenziali e degli oggetti al suo interno".
5. Impostare il periodo di conservazione per l'eliminazione temporanea.
6. Selezionare "Salva".

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Concedere le autorizzazioni di rimozione dei criteri di accesso a un'entità di sicurezza

1. Accedere al portale di Azure.
2. Cercare l'insieme di credenziali delle chiavi.
3. Selezionare "Criteri di accesso" nelle impostazioni.
4. Selezionare l'entità servizio a cui si vuole concedere l'accesso.
5. Per ogni elenco a discesa nelle autorizzazioni per chiavi, segreti e certificati, scorrere in basso fino a "Operazioni con privilegi" e selezionare l'autorizzazione "Ripulisci".

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="does-this-change-affect-me"></a>Quali sono gli effetti di questa modifica?

Se l'eliminazione temporanea è già stata attivata o se non si eliminano e si ricreano oggetti dell'insieme di credenziali delle chiavi con lo stesso nome, è probabile che non si noterà alcuna modifica nel comportamento dell'insieme di credenziali delle chiavi.

Se un'applicazione elimina e ricrea spesso oggetti dell'insieme di credenziali delle chiavi con le stesse convenzioni di denominazione, sarà necessario apportare modifiche alla logica dell'applicazione per mantenere il comportamento previsto. Vedere la sezione "Come rispondere alle modifiche di rilievo" precedente.

### <a name="how-do-i-benefit-from-this-change"></a>Quali sono i vantaggi di questa modifica?

La protezione dell'eliminazione temporanea fornirà all'organizzazione un ulteriore livello di protezione da eliminazioni accidentali o dannose. L'amministratore dell'insieme di credenziali delle chiavi può limitare l'accesso alle autorizzazioni di ripristino e a quelle di rimozione definitiva.

Se un utente elimina accidentalmente un insieme di credenziali delle chiavi o un segreto, è possibile concedergli autorizzazione di accesso per ripristinarlo autonomamente senza rischiare che lo elimini definitivamente. Questo processo self-service ridurrà al minimo il tempo di inattività nell'ambiente e garantirà la disponibilità dei segreti.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Come si può sapere se è necessario intervenire?

Seguire i passaggi precedenti nella sezione "Procedura per controllare gli insiemi di credenziali delle chiavi e verificare se l'eliminazione temporanea è attivata". Questa modifica influirà su qualsiasi insieme di credenziali delle chiavi per cui l'eliminazione temporanea non è attivata. Saranno presto disponibili strumenti aggiuntivi per il facilitare questo controllo e in quell'occasione questo documento verrà aggiornato.

### <a name="what-action-do-i-need-to-take"></a>Quali azioni è necessario intraprendere?

Assicurarsi che non sia necessario apportare modifiche alla logica dell'applicazione. Una volta ottenuta questa conferma, attivare l'eliminazione temporanea in tutti gli insiemi di credenziali delle chiavi. In questo modo ci si assicura che le modifiche di rilievo non avranno effetto quando alla fine dell'anno l'eliminazione temporanea verrà attivata per tutti gli insiemi di credenziali delle chiavi.

### <a name="by-when-do-i-need-to-take-action"></a>Entro quando è necessario intervenire?

L'eliminazione temporanea verrà attivata per tutti gli insiemi di credenziali delle chiavi entro la fine dell'anno. Per assicurarsi che le applicazioni non siano interessate, attivare l'eliminazione temporanea negli insiemi di credenziali delle chiavi il prima possibile.

## <a name="what-will-happen-if-i-dont-take-any-action"></a>Che cosa succede se non si interviene?

Se non si interviene, l'eliminazione temporanea verrà attivata automaticamente per tutti gli insiemi di credenziali delle chiavi alla fine dell'anno. Di conseguenza, si possono verificare errori di conflitto se si prova a eliminare un oggetto dell'insieme di credenziali delle chiavi e a ricrearlo con lo stesso nome senza prima rimuoverlo definitivamente dallo stato di eliminazione temporanea. In questo caso si possono verificare errori per le applicazioni o l'automazione.

## <a name="next-steps"></a>Passaggi successivi

- Per eventuali domande su questa modifica, inviare un messaggio all'indirizzo [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com).
- Leggere la [panoramica dell'eliminazione temporanea](soft-delete-overview.md)
