---
title: File di inclusione
description: File di inclusione
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335114"
---
| Risorsa | Limite |
| --- | --- |
| Gruppi di gestione per directory | 10,000 |
| Sottoscrizioni per gruppo di gestioneSubscriptions per management group | Senza limiti. |
| Livelli della gerarchia dei gruppi di gestione | Livello radice più 6 livelli<sup>1</sup> |
| Gruppo di gestione padre diretto per gruppo di gestioneDirect parent management group per management group | Uno |
| [Distribuzioni a livello](../articles/azure-resource-manager/templates/deploy-to-management-group.md) di gruppo di gestione per posizione | 800<sup>2</sup> |

<sup>1 : il</sup> nome del I 6 livelli non includono il livello di abbonamento.

<sup>2 Il</sup> nome del sistema Se si raggiunge il limite di 800 distribuzioni, eliminare le distribuzioni dalla cronologia che non sono più necessarie. Per eliminare le distribuzioni a livello di gruppo di gestione, utilizzare [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) o [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
