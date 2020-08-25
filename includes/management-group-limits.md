---
title: includere file
description: File di inclusione
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748854"
---
| Risorsa | Limite |
| --- | --- |
| Gruppi di gestione per tenant di Azure AD | 10,000 |
| Sottoscrizioni per gruppo di gestione | Senza limiti. |
| Livelli della gerarchia dei gruppi di gestione | Livello radice più 6 livelli<sup>1</sup> |
| Gruppo di gestione padre diretto per gruppo di gestione | Uno |
| [Distribuzioni a livello di gruppo di gestione](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per località | 800<sup>2</sup> |

<sup>1</sup>I 6 livelli non includono il livello di sottoscrizione.

<sup>2</sup>Se si raggiunge il limite di 800 distribuzioni, eliminare dalla cronologia le distribuzioni che non sono più necessarie. Per eliminare le distribuzioni a livello di gruppo di gestione, usare [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) o [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
