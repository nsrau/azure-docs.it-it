---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650669"
---
## <a name="1-download-the-file"></a>1. Scaricare il file

Eseguire i comandi seguenti. Copiare l'URL dei risultati nel browser per scaricare il file con estensione zip del profilo.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Estrarre il file con estensione zip

Estrarre il file con estensione zip. Il file contiene le cartelle seguenti:

* AzureVPN
* Generico
* OpenVPN (se sono state abilitate le impostazioni di autenticazione OpenVPN e Azure AD nel gateway). Per informazioni sul gateway VPN, vedere [Creare un tenant](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Per informazioni sulla rete WAN virtuale, vedere [Creare un tenant - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Recuperare le informazioni

Nella cartella **AzureVPN** selezionare il file ***azurevpnconfig.xml*** e aprirlo con Blocco note. Prendere nota del testo tra i tag seguenti.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli del profilo

Quando si aggiunge una connessione, è necessario specificare le informazioni raccolte nel passaggio precedente nella pagina dei dettagli del profilo. I campi corrispondono alle informazioni seguenti:

   * **Destinatari:** Identifica la risorsa di destinazione del token
   * **Autorità di certificazione:** Identifica il servizio token di sicurezza che ha rilasciato il token e il tenant di Azure AD
   * **Tenant:** Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token
   * **FQDN:** Nome di dominio completo del gateway VPN di Azure
   * **ServerSecret:** Chiave precondivisa del gateway VPN

## <a name="folder-contents"></a>Contenuto cartelle

* La **cartella generica** contiene il certificato del server pubblico e il file VpnSettings.xml. Il file VpnSettings.xml contiene le informazioni necessarie per configurare un client generico.

* Il file con estensione zip scaricato può contenere anche le cartelle **WindowsAmd64** e **WindowsX86**. Queste cartelle includono il programma di installazione per SSTP e IKEv2 per i client Windows. Per eseguire l'installazione, è necessario disporre dei diritti di amministratore per il client.
