---
title: Strumenti per usare Ansible con Azure
description: Installare e usare strumenti specifici per Ansible con Azure
ms.service: ansible
keywords: ansible, azure, devops, strumenti, vs code, visual studio code, estensione
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="tools-for-using-ansible-with-azure"></a>Strumenti per usare Ansible con Azure

Gli strumenti seguenti consentono di semplificare e rendere più efficiente l'uso di Ansible e Azure.

## <a name="visual-studio-code-extension-for-ansible"></a>Estensione Visual Studio Code per Ansible

L'[estensione Visual Studio Code per Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) fornisce diverse funzionalità per l'uso di Ansible da Visual Studio Code:

- Completamento automatico di direttive, moduli e i plug-in nella documentazione di Ansible.
- Visualizzazione di frammenti di codice facendo clic su &lt;CTRL>&lt;BARRA SPAZIATRICE> durante la creazione dei playbook Ansible.
- L'evidenziazione della sintassi consente di visualizzare il codice dei playbook Ansible in diversi colori e tipi di carattere in conformità con la sintassi YAML.
- I playbook Ansible possono essere eseguiti dalla finestra terminale di Visual Studio Code.
    - (Solo Windows) Ansible può essere eseguito da un contenitore Docker.
    - (Linux e macOS) Ansible può essere eseguito da un contenitore Docker o da un'installazione Ansible locale. 
- I playbook Ansible possono essere eseguiti da Azure Cloud Shell.