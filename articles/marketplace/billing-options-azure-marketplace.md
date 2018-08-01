---
title: Opzioni di fatturazione in Azure Marketplace | Azure
description: Opzioni di fatturazione in Azure Marketplace per gli editori.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: d0dbf603c600639679e121723556a7f3ceb17e37
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158982"
---
# <a name="billing-options-in-the-azure-marketplace"></a>Opzioni di fatturazione in Azure Marketplace

Questo articolo descrive le opzioni di fatturazione disponibili in [Azure Marketplace](https://azuremarketplace.microsoft.com).

## <a name="commercial-considerations-in-the-marketplace"></a>Considerazioni di natura commerciale su Marketplace
Marketplace non prevede la ripartizione del fatturato per i tipi di inserzioni seguenti: 
*   Elenco
*   Versione di valutazione
*   Transazioni che usano il modello di fatturazione Bring Your Own License (BYOL)

Non sono previste tariffe aggiuntive per la partecipazione alle vetrine in Marketplace.

Per altre informazioni, vedere [Criteri di partecipazione a Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies).  

## <a name="pay-as-you-go-and-byol-billing-options"></a>Opzioni di fatturazione con pagamento in base al consumo e Bring Your Own License
Quando si usa un modello di fatturazione con pagamento in base al consumo come opzione di pubblicazione, i ricavi risultanti dalla concessione di licenze software in base all'utilizzo vengono ripartiti in percentuale tra l'editore e Microsoft, nella proporzione di 80%/20%. Per una singola offerta è possibile usare sia il modello di fatturazione con pagamento in base al consumo sia il modello Bring Your Own License (BYOL). I due modelli di fatturazione coesistono a livello di offerta come SKU separati. Per configurare i modelli di fatturazione nell'offerta viene usato il portale Cloud Partner. 

Si considerino gli esempi seguenti:
*   Se si abilita l'opzione con pagamento in base al consumo, il risultato è il seguente:
    | Costo della licenza | € 1,00 all'ora |
    |:--- |:--- |
    | Costo dell'utilizzo di Azure (D1/1-Core) | € 0,14 all'ora |
    | *Importo addebitato da Microsoft al cliente* | *€ 1,14 all'ora* |

    In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di VM pubblicata:
    | Microsoft addebita | € 1,14 all'ora |
    |:--- |:--- |
    | Microsoft paga l'80% del costo della licenza | € 0,80 all'ora |
    | Microsoft trattiene il 20% del costo della licenza | € 0,20 all'ora |
    | Microsoft trattiene il costo dell'utilizzo di Azure | € 0,14 all'ora |

*   Se si abilita l'opzione BYOL, il risultato è il seguente:
    | Costo della licenza | Il costo della licenza viene concordato e addebitato dall'utente |
    |:--- |:--- |
    | Costo dell'utilizzo di Azure (D1/1-Core) | € 0,14 all'ora |
    | *Importo addebitato da Microsoft al cliente* | *€ 0,14 all'ora* |

    In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di VM pubblicata: 
    | Microsoft addebita | € 0,14 all'ora |
    |:--- |:--- |
    | Microsoft trattiene il costo dell'utilizzo di Azure | € 0,14 all'ora |
    | Microsoft trattiene lo 0% del costo della licenza | € 0,00 all'ora |

## <a name="single-billing-and-payment-methods"></a>Metodi di pagamento e fatturazione singola
Un importante vantaggio offerto dall'opzione di pubblicazione con transazione in Marketplace è dato del fatto che i costi di gestione delle licenze e dell'utilizzo di Azure vengono addebitati direttamente al cliente in singola fattura.

In questo scenario Microsoft addebita e riscuote per conto dell'editore. La fatturazione da parte di Microsoft elimina il requisito di stabilire un accordo di approvvigionamento diretto con il cliente. La modalità di fatturazione singola può consentire all'editore di risparmiare tempo e risorse, oltre a concentrarsi maggiormente sulle strategie di vendita anziché occuparsi dell'attività di fatturazione. 

## <a name="enterprise-agreement"></a>Enterprise Agreement  
I clienti con contratto Microsoft Enterprise Agreement possono usare questo contratto per effettuare i pagamenti relativi ai prodotti Microsoft. Ai clienti può essere addebitato il costo dei prodotti, compreso quello relativo all'utilizzo di Azure. La possibilità di usare il contratto Enterprise per i pagamenti è stata pensata per le organizzazioni che vogliono usufruire di licenze relative a software e servizi cloud per un periodo di almeno tre anni. In questo modo è possibile ripartire i pagamenti su più periodi anziché effettuare un unico pagamento iniziale. Quando si usa l'opzione di pubblicazione con pagamento in base al consumo, la fatturazione relativa ai costi di licenza software viene eseguita in base al ciclo di fatturazione trimestrale delle eccedenze del contratto Enterprise.  

### <a name="monetary-commitment"></a>Impegno monetario
I clienti con contratto Enterprise possono aggiungere Azure al contratto. È possibile aggiungere Azure al contratto definendo un impegno monetario iniziale per Azure. L'impegno monetario verrà usato nel corso dell'anno. L'impegno include una qualsiasi combinazione di utilizzo dei servizi di Azure.

## <a name="next-steps"></a>Passaggi successivi
Rivedere la [Guida alla pubblicazione per Azure Marketplace e AppSource](./marketplace-publishers-guide.md).
