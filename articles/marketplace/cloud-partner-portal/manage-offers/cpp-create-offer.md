---
title: Creare offerte di Marketplace | Azure Marketplace
description: Creare offerte in Azure Marketplace e nel marketplace di AppSource usando il portale Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pabutler
ms.openlocfilehash: cc0d8e68578500d4fa5f1b167790d60c8f7c5067
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942168"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Creare offerte di Azure Marketplace e AppSource

Uno degli scopi principali del portale Cloud Partner è consentire agli autori di creare (e quindi pubblicare) offerte in Microsoft Azure Marketplace e nel marketplace di AppSource.  Questa operazione inizia sempre con la scelta del tipo di offerta desiderato dal [menu Nuova offerta](../portal-tour/cpp-new-offer-menu.md).  In risposta viene visualizzata la pagina **Nuova offerta** appropriata per il tipo di offerta selezionato.  Ad esempio, l'immagine seguente mostra la pagina **Nuova offerta** predefinita per un tipo di applicazione di Azure.

![Pagina Nuova offerta predefinita](./media/new-offer-page.png)

Nella barra dei menu orizzontale visualizzata nella parte superiore della pagina sono disponibili due opzioni di scheda: 
- Scheda **Editor**: consente l'immissione di informazioni e il caricamento degli asset per la nuova istanza di offerta.  Questa scheda viene visualizzata per impostazione predefinita.
- Scheda **Stato**: indica lo stato di pubblicazione e contiene l'elenco di tutti i problemi di convalida e revisione. 

Quando si crea un'offerta, è necessario usare la scheda **Editor** per immettere le relative informazioni. 

## <a name="editing-operations"></a>Operazioni di modifica

Nella barra orizzontale sopra l'area di input dei dati vengono visualizzati i pulsanti seguenti:

|   Pulsante    |   Scopo                                                          |
|   ------    |  --------                                                          |
| **Salva**    | Salva tutte le modifiche recenti apportate ai dati.  È necessario salvare manualmente le modifiche prima uscire dalla pagina, altrimenti andranno perse. | 
| **Annulla** | Annulla le modifiche recenti apportate ai dati (dall'ultimo salvataggio).             |
| **Confronta** | Confronta lo stato dell'offerta corrente con l'offerta pubblicata.  Questo pulsante viene abilitato solo dopo la pubblicazione di un'offerta.  |
| **Pubblica** | Avvia il processo di pubblicazione dell'offerta.                       |
| **Elimina**  | Elimina l'offerta dopo che è stata creata ma prima della pubblicazione. |
|   |   |


## <a name="editing-tabs"></a>Schede di modifica

Quando si crea un'offerta, è necessario specificare i dati obbligatori e facoltativi in ogni scheda che si trova nella colonna verticale a sinistra della pagina **Nuova offerta**.  Per la raccolta dati vengono visualizzati i controlli standard dell'interfaccia utente, ad esempio caselle di testo, menu a discesa e caselle di controllo.  Anche se la raccolta specifica delle schede di modifica dipende dal tipo di offerta, la tabella seguente elenca alcune delle schede più comuni.

|      Nome scheda       |   Scopo                                                            |
|      --------       |   -------                                                            |
| **Impostazioni dell'offerta**  | Raccoglie le informazioni relative all'offerta e all'identità dell'editore.                    |
| **SKU**            | Definisce le caratteristiche tecniche e aziendali di ogni versione SKU (Stock-Keeping Unit) dell'offerta. |
| **Test Drive**      | Per i tipi di offerta che supportano questa funzionalità facoltativa, definisce una dimostrazione dell'offerta.  Per altre informazioni, vedere [Informazioni sul test drive](../test-drive/what-is-test-drive.md).  |
| **Marketplace** o **Vetrina** | Raccoglie le stringhe di testo, le immagini e i documenti usati per inserire l'offerta nel marketplace. |
| **Supporto**         | Raccoglie le informazioni di contatto per l'assistenza clienti, il supporto tecnico e quello online.  |
|  |  |

Il contenuto delle schede con nome simile può differire in base ai diversi tipi di offerta.  I dettagli specifici di un'offerta di queste schede sono disponibili all'interno della sezione "Crea offerta" per ogni tipo di offerta.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato e salvato un'offerta e prima o dopo la pubblicazione, è possibile [visualizzarne lo stato](./cpp-view-status-offer.md).
