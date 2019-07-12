---
title: Terminologia di anteprima di condivisione dei dati di Azure
description: Terminologia di anteprima di condivisione dei dati di Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789228"
---
# <a name="azure-data-share-preview-concepts"></a>Concetti di anteprima di condivisione dei dati di Azure 

Anteprima di condivisione dei dati Azure introduce alcuni nuovi termini correlati alla condivisione dei dati. Questo articolo illustra che alcuni termini che possono vedere utilizzati in tutto il servizio utilizzati di frequente. 

## <a name="data-provider"></a>Provider di dati

Un Provider di dati è l'organizzazione che condivide i dati con i relativi utenti. In genere il provider di dati può essere un proprietario o un addetto dei dati. Provider di dati da condividere i dati di vario tipo. Alcuni dati di un provider di dati potrebbe essere necessario condividere esempi di dati non elaborati, ad esempio le vendite o i dati delle serie temporali. Un provider di dati potrebbe inoltre desidera condividere dati pre-elaborati, curati che già contiene informazioni dettagliate e analitica. 

## <a name="data-consumer"></a>Consumer di dati 

Un Consumer di dati è l'organizzazione che riceve dati da un provider di dati. Il consumer di dati potrebbe vogliono unire i dati condivisi con i propri dati per ricavare informazioni dettagliate. In alcuni casi, il consumer di dati può ricevere dati che sono già stati elaborati. 

## <a name="data-share"></a>Condivisione dei dati

Una condivisione di dati è un gruppo di set di dati condivisi come una singola entità. I set di dati possono provenire da un numero di origini dati di Azure supportati da una condivisione dei dati di Azure. Condivisione dei dati di Azure supporta attualmente, archiviazione Blob di Azure e Azure Data Lake Store. 

## <a name="share-subscription"></a>Sottoscrizione di condivisione 

Quando un consumer di dati viene accettato un invito alla condivisione dei dati da un provider di dati, viene creata una sottoscrizione di condivisione. Provider di dati possono visualizzare le sottoscrizioni attive, passare a **condivisioni inviato** nei propri dati Azure condividere account e selezionando **condividere le sottoscrizioni**.

Un consumer di dati è possibile controllare se hanno una sottoscrizione di condivisione attivi, passare a **ricevuti condivisioni** e visualizzando lo stato delle proprie azioni ricevuti. 

## <a name="snapshot"></a>Snapshot

È possibile creare uno snapshot da un consumer di dati dopo aver accettato un invito alla condivisione dei dati. Dopo aver accettato un invito, possono attivare uno snapshot completo dei dati condivisi con loro. Lo snapshot è una copia dei dati in corrispondenza del punto nel tempo che il consumer di dati generato lo snapshot. 

Esistono due tipi di snapshot - completi e incrementali. Uno snapshot completo contiene tutti i dati all'interno della condivisione di dati. Uno snapshot incrementale contiene tutti i dati che sono stato aggiornato o aggiunto poiché è stata attivata l'ultimo snapshot. 

## <a name="snapshot-settings-in-azure-data-share"></a>Impostazioni di snapshot di condivisione dei dati di Azure
 
Un provider di dati è possibile abilitare un'impostazione di snapshot per una condivisione di dati. Questa impostazione consente ai consumer di dati ricevere gli aggiornamenti incrementali appena si verificano. Questa impostazione deve essere abilitata se il provider di dati vuole che i consumer di dati per ricevere gli aggiornamenti ai dati che sono stato condiviso. 

Se un provider di dati abilita questa impostazione, è possibile selezionare un intervallo di ricorrenza. L'intervallo di ricorrenza può essere oraria o giornaliera. 

Un consumer di dati ha la possibilità di acconsentire esplicitamente a questa pianificazione di snapshot per ricevere gli aggiornamenti incrementali, che include tutti i dati che sono stato modificato dopo che generati prima di tutto un nuovo snapshot. 

## <a name="invitation"></a>Invito

Un provider di dati può invitare più destinatari per la condivisione dei dati. È possibile farlo mediante l'aggiunta di destinatari per la condivisione dei dati. Gli inviti possono essere aggiunti anche dopo aver creata una condivisione di dati. 

Un provider di dati è possibile eliminare un invito dopo che è stata inviata. Si noti che se un provider di dati Elimina un invito dopo che è stato accettato, il consumer di dati può comunque avere una sottoscrizione di condivisione attivi. Se il provider di dati Elimina un invito e non è ancora stato accettato, il consumer di dati non sarà in grado di accettare il documento. 

## <a name="recipient"></a>Recipient

Un destinatario è un utente che riceve un invito a una condivisione di dati. In genere, un provider di dati viene aggiunto ai destinatari alla condivisione di dati che creano. Una volta che il destinatario dell'invito ha accettato l'invito, diventano un consumer di dati.  

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come avviare la condivisione dei dati, il [condividere i dati](share-your-data.md) esercitazione.

