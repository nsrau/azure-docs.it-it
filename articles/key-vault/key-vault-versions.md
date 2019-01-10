---
title: Versioni di Key Vault
description: Le diverse versioni di Azure Key Vault
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e8622dcc-59a3-4f4b-9f63-cd2232515a65
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 7671452e8b5226d3c3779bbe86e23b8cffe1a769
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079052"
---
# <a name="key-vault-versions"></a>Versioni di Key Vault

## <a name="2016-10-01---managed-storage-account-keys"></a>01-10-2016 - Chiavi dell'account di archiviazione gestito

Estate 2017 - La funzionalità Chiavi dell'account di archiviazione ha semplificato ulteriormente l'integrazione con Archiviazione di Azure. Per altre informazioni, vedere l'argomento introduttivo [Chiavi dell'account di archiviazione Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys).

## <a name="2016-10-01---soft-delete"></a>01-10-2016 - Eliminazione temporanea

Estate 2017 - Aggiunta di una funzione di eliminazione temporanea per migliorare la protezione dei dati degli insiemi di credenziali delle chiavi e dei relativi oggetti. Per altre informazioni, vedere l'argomento introduttivo [Panoramica di eliminazione temporanea di Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).

## <a name="2015-06-01---certificate-management"></a>01-06-2015 - Gestione certificati

Gestione certificati viene aggiunto come funzionalità alla versione con disponibilità generale del 01-06-2015 il 26 settembre 2016.

## <a name="2015-06-01---general-availability"></a>01-06-2015 - Disponibilità generale

Versione con disponibilità generale del 01-06-2015, annunciata il 24 giugno 2015.

In questa versione sono state apportate le modifiche seguenti:

- Eliminare una chiave: campo "Usa" rimosso.
- Ottenere informazioni relative a una chiave: campo "Usa" rimosso.
- Importare una chiave in un insieme di credenziali: campo "Usa" rimosso.
- Ripristinare una chiave: campo "Usa" rimosso.
- “RSA_OAEP” modificato in “RSA-OAEP” per gli algoritmi RSA. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md).

## <a name="2015-02-01-preview"></a>2015-02-01-preview 

Seconda versione di anteprima 2015-02-01-preview, annunciata il 20 aprile 2015. Per altre informazioni, vedere il post di blog [REST API Update](http://blogs.technet.com/b/kv/archive/2015/04/20/empty-3.aspx) (Aggiornamento API REST).

Sono state aggiornate le attività seguenti:

- Elencare le chiavi in un insieme di credenziali - aggiunta del supporto per la paginazione all'operazione.
- Elencare le versioni di una chiave - aggiunta dell'operazione per elencare le versioni di una chiave.
- Elencare i segreti in un insieme di credenziali - aggiunta del supporto per la paginazione.
- Elencare le versioni di un segreto - aggiunta di un'operazione per elencare le versioni di un segreto.
- Tutte le operazioni - aggiunta del timestamp di creazione/aggiornamento agli attributi.
- Creare un segreto - aggiunta di Content-Type ai segreti.
- Creare una chiave - aggiunta di tag come informazioni facoltative.
- Creare un segreto - aggiunta di tag come informazioni facoltative.
- Aggiornare una chiave - aggiunta di tag come informazioni facoltative.
- Aggiornare un segreto - aggiunta di tag come informazioni facoltative.
- Modifica delle dimensioni massime dei segreti da 10 K a 25 kB. Vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md).

## <a name="2014-12-08-preview"></a>2014-12-08-preview

Prima versione di anteprima 2014-12-08-preview, annunciata l'8 gennaio 2015.

## <a name="see-also"></a>Vedere anche 
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
