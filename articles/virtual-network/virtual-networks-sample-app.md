<properties
   pageTitle="Applicazione di esempio da usare per ambienti con limiti di sicurezza | Microsoft Azure"
   description="Distribuire questa semplice applicazione Web dopo aver creato una rete perimetrale per testare gli scenari di flusso del traffico"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# Applicazione di esempio da usare per ambienti con limiti di sicurezza

[Tornare alla pagina relativa alle procedure consigliate sui limiti di sicurezza][HOME]

Questi script di PowerShell possono essere eseguiti localmente nei server IIS01 e AppVM01 per installare e configurare un'applicazione Web molto semplice in grado di visualizzare una pagina HTML dal server IIS01 front-end con contenuto del server AppVM01 back-end.

Tale app fornirà un semplice ambiente di testing per molti degli esempi di rete perimetrale e per le modalità con cui le modifiche relative a endpoint, gruppi di sicurezza di rete, routing definito dall'utente e regole del firewall possono influire sui flussi di traffico.

## Regola del firewall per consentire il traffico ICMP
Questa semplice istruzione PowerShell può essere eseguita su qualsiasi macchina virtuale Windows per consentire il traffico ICMP (Ping). Sarà quindi più facile eseguire il testing e la risoluzione dei problemi perché il protocollo ping potrà passare attraverso Wirewall Firewall. Per la maggior parte delle distribuzioni Linux, ICMP è attivato per impostazione predefinita.

	# Turn On ICMPv4
	New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
		-Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Nota:** se si usano gli script seguenti, l'aggiunta di questa regola del firewall corrisponde alla prima istruzione.

## IIS01 - Script di installazione dell'applicazione Web
Questo script consentirà di eseguire queste operazioni:

1.	Aprire IMCPv4 (Ping) in Windows Firewall nel server locale per eseguire più facilmente il testing.
2.	Installare IIS e .Net Framework v4.5.
3.	Creare una pagina Web ASP.NET e un file Web.config.
4.	Modificare il pool di applicazioni predefinito per semplificare l'accesso ai file.
5.	Impostare l'utente anonimo per l'account amministratore e la relativa password.

Lo script di PowerShell deve essere eseguito localmente mentre viene usata una sessione RDP per accedere a IIS01.

	# IIS Server Post Build Config Script
	# Get Admin Account and Password
		Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
		$theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
		$thePassword = Read-Host -Prompt "The Admin Password"
		
	# Turn On ICMPv4
		Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
		New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
		
	# Install IIS
		Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
		add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
		
	# Create Web App Pages
		Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
		$MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
		<%@ Import Namespace="System.IO" %>
		<script language="vb" runat="server">
			Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
			    Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
			    Dim objStreamReader As StreamReader
			    objStreamReader = File.OpenText(FILENAME)
			    Dim contents As String = objStreamReader.ReadToEnd()
			    lblOutput.Text = contents
			    objStreamReader.Close()
		        lblTime.Text = Now()
			End Sub
		</script>
			
		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head runat="server">
			<title>DMZ Example App</title>
		</head>
		<body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
		  <form id="frmMain" runat="server">
		    <div>
		      <h1>Looks like you made it!</h1>
		      This is a page from the inside (a web server on a private network),<br />
		      and it is making its way to the outside! (If you are viewing this from the internet)<br />
		      <br />
		      The following sections show:
		      <ul style="margin-top: 0px;">
		        <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
		        <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
		        <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
		      </ul>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
		      <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
		        <b>Image File Linked from the Internet</b>:<br />
		        <br />
		        <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
		    </div>
		  </form>
		</body>
		</html>'
		
		$WebConfig ='<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		  <system.web>
		    <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
		    <httpRuntime targetFramework="4.5" />
		    <identity impersonate="true" />
		    <customErrors mode="Off"/>
		  </system.web>
		  <system.webServer>
		    <defaultDocument>
		      <files>
		        <add value="Home.aspx" />
		      </files>
		    </defaultDocument>
		  </system.webServer>
		</configuration>'
			
		$MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
		$WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
	
	# Set App Pool to Clasic Pipeline to remote file access will work easier
		Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
		c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
		c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
		
	# Make sure the IIS settings take
		Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
		Restart-Service -Name W3SVC
		
		Write-Host
		Write-Host "Web App Creation Successfull!" -ForegroundColor Green
		Write-Host


## AppVM01 - Script di installazione del file server
Imposta il back-end per questa semplice applicazione. Questo script consentirà di eseguire queste operazioni:

1.	Aprire IMCPv4 (Ping) nel firewall per eseguire più facilmente il testing.
2.	Creare una nuova directory.
3.	Creare un file di testo accessibile in remoto tramite la pagina Web sopra indicata.
4.	Impostare su Anonimo le autorizzazioni per la directory e il file in modo da consentire l'accesso.
5.	Disattivare la sicurezza avanzata di Internet Explorer per facilitare l'esplorazione da questo server. 

>[AZURE.IMPORTANT] **Procedura consigliata**: non disattivare mai la sicurezza avanzata di Internet Explorer in un server di produzione. Inoltre, è generalmente preferibile non esplorare il Web da un server di questo tipo. Anche l'apertura di condivisioni file per l'accesso anonimo è un'attività da evitare, ma in questo caso viene eseguita per semplificare le operazioni.

Lo script di PowerShell deve essere eseguito localmente mentre viene usata una sessione RDP per accedere ad AppVM01. Per essere certi che la procedura abbia esito positivo, è necessario eseguire PowerShell come amministratori.
	
	# AppVM01 Server Post Build Config Script
	# PowerShell must be run as Administrator for Net Share commands to work
	
	# Turn On ICMPv4
	    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
	
	# Create Directory
	    New-Item "C:\WebShare" -ItemType Directory
	
	# Write out Rand.txt
	    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
	    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
	
	# Set Permissions on share
	    $Acl = Get-Acl "C:\WebShare"
	    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
	    $Acl.SetAccessRule($AccessRule)
	    Set-Acl "C:\WebShare" $Acl
	
	# Create network share
	    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
	
	# Turn Off IE Enhanced Security Configuration for Admins
	    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
	
	    Write-Host
	    Write-Host "File Server Setup Successfull!" -ForegroundColor Green
	    Write-Host
	

## DNS01 - Script di installazione del server DNS
In questa applicazione di esempio non è incluso alcuno script per la configurazione del server DNS. Se il testing delle regole del firewall, del gruppo di sicurezza di rete o del routing definito dall'utente deve includere il traffico DNS, sarà perciò necessario configurare il server DNS01 manualmente. Il file XML di configurazione di rete per entrambi gli esempi include DNS01 come server DNS primario e il server DNS pubblico ospitato dal livello 3 come server DNS di backup. Il server DNS del livello 3 sarà l'effettivo server DNS usato per il traffico non locale e, se DNS01 non è configurato, non si disporrà di alcun DNS locale.

<!--Link References-->
[HOME]: ../best-practices-network-security.md

<!---HONumber=AcomDC_0204_2016-->