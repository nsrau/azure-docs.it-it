<properties linkid="develop-java-how-to-guides-web-sso" urlDisplayName="Web SSO" pageTitle="Single sign-on with Azure Active Directory (Java)" metaKeywords="Azure Java web app, Azure single sign-on, Azure Java Active Directory" description="Learn how to create a Java web application that uses single sign-on with Azure Active Directory." metaCanonical="" services="active-directory" documentationCenter="Java" title="Web Single Sign-On with Java and Azure Active Directory" authors="" solutions="" manager="" editor="" />

Single Sign-On Web con Java e Azure Active Directory
====================================================

Introduzione
------------

In questa esercitazione viene illustrato agli sviluppatori Java come sfruttare Azure Active Directory per abilitare Single Sign-On per gli utenti di Office 365. Si apprenderà come:

-   Eseguire il provisioning dell'applicazione Web nel tenant del cliente
-   Proteggere l'applicazione mediante WS-Federation

### Prerequisiti

In questa esercitazione viene utilizzato uno specifico server applicazioni, ma gli sviluppatori Java più esperti possono applicare il processo descritto di seguito anche ad altri ambienti. Per questa esercitazione sono necessari i prerequisiti seguenti nell'ambiente di sviluppo:

-   [Codice di esempio Java per Azure Active Directory](https://github.com/WindowsAzure/azure-sdk-for-java-samples/tree/master/WAAD.WebSSO.JAVA)
-   [Java Runtime Environment 1.6](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
-   [Server applicazioni JBoss versione 7.1.1.Final](http://www.jboss.org/jbossas/downloads/)
-   [JBoss Developer Studio IDE](https://devstudio.jboss.com/earlyaccess/)
-   Internet Information Services (IIS) 7.5 con SSL abilitato
-   Windows PowerShell
-   [Cmdlet di Office 365 PowerShell](http://onlinehelp.microsoft.com/en-us/office365-enterprises/ff652560.aspx)

### Sommario

-   [Introduzione](#introduction)
-   [Passaggio 1: Creare un'applicazione Java](#createapp)
-   [Passaggio 2: Eseguire il provisioning dell'applicazione nel tenant della directory della società](#provisionapp)
-   [Passaggio 3: Proteggere l'applicazione mediante WS-Federation per l'accesso dei dipendenti](#protectapp)
-   [Riepilogo](#summary)

Passaggio 1: Creare un'applicazione Java
----------------------------------------

In questo passaggio viene descritto come creare una semplice applicazione Java che rappresenterà una risorsa protetta. L'accesso a questa risorsa verrà concesso tramite autenticazione federata gestita dal servizio token di sicurezza della società, descritto più avanti nell'esercitazione.

1.  Aprire una nuova istanza di JBoss Developer Studio.
2.  Scegliere **New** dal menu **File** e quindi fare clic su **Project**.
3.  Nella finestra di dialogo **New Project** espandere la cartella **Maven**, fare clic su **Maven Project** e quindi su **Next**.
4.  Nella finestra di dialogo **New Maven Project** selezionare **Create a simple project (skip archetype selection)** e quindi fare clic su **Next**.
5.  Nella pagina successiva della finestra di dialogo **New Maven Project** digitare *sample* nelle caselle di testo **Group Id** e **Artifact Id**. Selezionare quindi **war** dal menu a discesa **Packaging** e fare clic su **Finish**.
6.  Verrà creato il nuovo progetto Maven. Nel menu **Project Explorer** sulla sinistra espandere il progetto **sample**, fare clic con il pulsante destro del mouse sul file **pom.xml**, fare clic su **Open With** e quindi su **Text Editor**.
7.  Nel file **pom.xml** aggiungere il codice XML seguente nella sezione *&lt;project\>*:

         <repositories>
             <repository>
                 <id>jboss-public-repository-group</id>
                 <name>JBoss Public Maven Repository Group</name>
                 <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                 <layout>default</layout>
                 <releases>
                     <enabled>true</enabled>
                     <updatePolicy>never</updatePolicy>
                 </releases>
                 <snapshots>
                     <enabled>true</enabled>
                     <updatePolicy>never</updatePolicy>
                 </snapshots>
             </repository>
         </repositories>
         <pluginRepositories>
             <pluginRepository>
                 <id>jboss-public-repository-group</id>
                 <name>JBoss Public Maven Repository Group</name>
                 <url>https://repository.jboss.org/nexus/content/groups/public-jboss/</url>
                 <layout>default</layout>
                 <releases>
                     <enabled>true</enabled>
                     <updatePolicy>always</updatePolicy>
                 </releases>
                 <snapshots>
                     <enabled>true</enabled>
                     <updatePolicy>always</updatePolicy>
                 </snapshots>
             </pluginRepository>
         </pluginRepositories>
         <build>
             <plugins>
                 <plugin>
                     <groupId>org.apache.maven.plugins</groupId>
                     <artifactId>maven-compiler-plugin</artifactId>
                     <version>2.0.2</version>
                     <configuration>
                         <source>1.6</source>
                         <target>1.6</target>
                     </configuration>
                 </plugin>
             </plugins>
         </build> 

    Dopo aver inserito questo codice XML salvare il file **pom.xml**.

8.  Fare clic con il pulsante destro del mouse sul progetto **sample**, quindi scegliere **Maven** e fare clic su **Update Maven Project**. Nella finestra di dialogo **Update Maven Project** fare clic su **OK**. Con questo passaggio il progetto verrà aggiornato con le modifiche apportate a **pom.xml**.

9.  Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **New** e quindi fare clic su **JSP File**.

10. Nella finestra di dialogo **New JSP File** modificare il percorso per il nuovo file in *sample/src/main/webapp*. Assegnare quindi al file il nome **index.jsp** e fare clic su **Finish**.

11. Il nuovo file **index.jsp** verrà aperto automaticamente. Sostituire il codice generato automaticamente con il codice seguente e quindi salvare il file:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index</title>
        </head>
        <body>
            <h3>Index Page</h3>
        </body>
        </html> 

12. Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **Run As** e quindi fare clic su **Run on Server**.

13. Nella finestra di dialogo **Run On Server** assicurarsi che **JBoss Enterprise Application Platform 6.x** sia selezionato e quindi fare clic su **Finished**.

Passaggio 2: Eseguire il provisioning dell'applicazione nel tenant della directory della società
------------------------------------------------------------------------------------------------

In questo passaggio viene descritto il provisioning dell'applicazione Java eseguito da un amministratore di Azure Active Directory nel tenant di un cliente e la configurazione di Single Sign-On. Dopo avere completato questo passaggio, i dipendenti della società potranno eseguire l'autenticazione all'applicazione Web utilizzando gli account Office 365.

Il processo di provisioning inizia con la creazione di una nuova entità servizio per l'applicazione. In Azure Active Directory le entità servizio vengono utilizzate per registrare e autenticare le applicazioni per la directory.

1.  Se necessario, scaricare e installare i cmdlet di Office 365 PowerShell.
2.  Nel menu **Start** eseguire la console **Modulo dei Microsoft Online Services per Windows PowerShell**. Questa console offre un ambiente da riga di comando per la configurazione di attributi relativi al tenant di Office 365, ad esempio la creazione e la modifica di entità servizio.
3.  Per importare il modulo **MSOnlineExtended** richiesto, digitare il comando seguente e premere INVIO:

         Import-Module MSOnlineExtended -Force

4.  Per connettersi alla directory di Office 365, è necessario fornire le credenziali di amministratore della società. Digitare il comando seguente e premere INVIO, quindi immettere le credenziali quando richiesto:

         Connect-MsolService

5.  A questo punto è possibile creare una nuova entità servizio per l'applicazione. Digitare il comando seguente e premere INVIO:

         New-MsolServicePrincipal -ServicePrincipalNames @("javaSample/localhost") -DisplayName "Federation Sample Web Site" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 

    In questo passaggio verranno visualizzate informazioni simili alle seguenti:

         The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
         DisplayName           : Federation Sample Java Web Site
         ServicePrincipalNames : {javaSample/localhost}
         ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
         AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
         TrustedForDelegation  : False
         AccountEnabled        : True
         KeyType               : Symmetric
         KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
         StartDate             : 12/01/2012 08:00:00 a.m.
         EndDate               : 12/01/2013 08:00:00 a.m.
         Usage                 : Verify 

    > [WACOM.NOTE] È consigliabile salvare l'output, in particolare la chiave simmetrica generata. Questa chiave viene visualizzata solo durante la creazione dell'entità servizio e non sarà recuperabile in seguito. Gli altri valori sono necessari per utilizzare l'API Graph per leggere e scrivere informazioni nella directory.

6.  Con l'ultimo passaggio viene impostato l'URL di risposta per l'applicazione, ossia l'indirizzo a cui vengono inviate le risposte in seguito ai tentativi di autenticazione. Digitare i comandi seguenti e premere INVIO:

         $replyUrl = New-MsolServicePrincipalAddresses -Address "https://localhost:8443/sample" 

         Set-MsolServicePrincipal -AppPrincipalId "7829c758-2bef-43df-a685-717089474505" -Addresses $replyUrl 

A questo punto, il provisioning dell'applicazione Web è stato completato nella directory ed è possibile utilizzarla per consentire ai dipendenti della società l'accesso Single Sign-On al Web.

Passaggio 3: Proteggere l'applicazione mediante WS-Federation per l'accesso dei dipendenti
------------------------------------------------------------------------------------------

In questo passaggio viene illustrato come aggiungere l'accesso federato utilizzando Windows Identity Foundation (WIF) e la libreria **waad-federation** scaricata come parte del pacchetto di codice di esempio nei prerequisiti. Verrà inoltre aggiunta una pagina di accesso e verranno configurati i criteri di attendibilità tra l'applicazione e il tenant della directory.

1.  In JBoss Developer Studio fare clic su **File** e quindi su **Import**.

2.  Nella finestra di dialogo **Import** espandere la cartella **Maven**, fare clic su **Existing Maven Projects** e quindi su **Next**.

3.  Nella finestra di dialogo **Import Maven Projects** impostare il percorso **Root Directory** su quello in cui è stata scaricata la libreria **waad-federation** nel codice di esempio. Selezionare quindi la casella di controllo accanto al file **pom.xml** dal progetto **waad-federation** e fare clic su **Finish**.

4.  Espandere il progetto **sample**, fare clic con il pulsante destro del mouse sul file **pom.xml**, scegliere **Open With** e quindi fare clic su **Text Editor**.

5.  Nel file **pom.xml** aggiungere il codice XML seguente nella sezione *&lt;project\>* e quindi salvare il file:

         <dependencies>
             <dependency>
                 <groupId>javax.servlet</groupId>
                 <artifactId>servlet-api</artifactId>
                 <version>3.0-alpha-1</version>
             </dependency>
             <dependency>
                 <groupId>com.microsoft.samples.waad.federation</groupId>
                 <artifactId>waad-federation</artifactId>
                 <version>0.0.1-SNAPSHOT</version>
             </dependency>
         </dependencies> 

6.  Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **Maven** e quindi fare clic su **Update Project**. Nella finestra di dialogo **Update Maven Project** fare clic su **OK**. Con questo passaggio il progetto verrà aggiornato con le modifiche apportate a **pom.xml**.

7.  Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **New** e quindi fare clic su **Filter**.

8.  Nella finestra di dialogo **Create Filter** digitare *FederationFilter* per la voce **Class name** e quindi fare clic su **Finish**.

9.  Verrà aperto il file **FederationFilter.java** generato automaticamente. Sostituire il codice con quello seguente e quindi salvare il file.

         import java.io.IOException;
         import javax.servlet.Filter;
         import javax.servlet.FilterChain;
         import javax.servlet.FilterConfig;
         import javax.servlet.ServletException;
         import javax.servlet.ServletRequest;
         import javax.servlet.ServletResponse;
         import javax.servlet.http.HttpServletRequest;
         import javax.servlet.http.HttpServletResponse;
         import java.util.regex.*;
         import com.microsoft.samples.federation.FederatedLoginManager; import com.microsoft.samples.federation.URLUTF8Encoder;

         public class FederationFilter implements Filter {
             private String loginPage;
             private String allowedRegex;
             public void init(FilterConfig config) throws ServletException {
                 this.loginPage = config.getInitParameter("login-page-url");
                 this.allowedRegex = config.getInitParameter("allowed-regex");
             }
             public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
                     if (request instanceof HttpServletRequest) { 
                         HttpServletRequest httpRequest = (HttpServletRequest) request;
                         if (!httpRequest.getRequestURL().toString().contains(this.loginPage)) {
                             FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(httpRequest);
                             boolean allowedUrl = Pattern.compile(this.allowedRegex).matcher(httpRequest.getRequestURL().toString()).find();
                             if (!allowedUrl && !loginManager.isAuthenticated()) {
                                 HttpServletResponse httpResponse = (HttpServletResponse) response;
                                 String encodedReturnUrl = URLUTF8Encoder.encode(httpRequest.getRequestURL().toString());
                                 httpResponse.setHeader("Location", this.loginPage + "
        returnUrl=" + encodedReturnUrl);
                                 httpResponse.setStatus(302);
                                 return;
                             }
                         }
                     }
                 chain.doFilter(request, response);
             }
             public void destroy() {
             }
         } 

10. In **Project Explorer** espandere le cartelle **src**, **main** e **webapp**. Fare clic con il pulsante destro del mouse sul file **web.xml**, scegliere **Open With** e quindi fare clic su **Text Editor**.

11. Nel file **web.xml** verrà aggiunto un filtro per gestire le pagine protette e non protette, che reindirizzerà inoltre gli utenti non autenticati alla pagina di accesso (specificata nel parametro del filtro **login-page-url**). Non verrà tuttavia filtrato un URL che corrisponde al regex specificato nel parametro del filtro **allowed-regex**. Aggiungere il codice XML seguente nella sezione *&lt;web-app\>* e quindi salvare il file **web.xml**.

        <filter>
            <filter-name>FederationFilter</filter-name>
            <filter-class>FederationFilter</filter-class>
            <init-param>
                <param-name>login-page-url</param-name>
                <param-value>/sample/login.jsp</param-value>
            </init-param>
            <init-param>
                <param-name>allowed-regex</param-name>
                <param-value>(\/sample\/login.jsp|\/sample\/wsfed-saml|\/sample\/oauth)</param-value>
            </init-param>
        </filter>
        <filter-mapping>
            <filter-name>FederationFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping> 

12. Per creare una pagina di accesso, fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **New** e quindi fare clic su **JSP File**.

13. Nella finestra di dialogo **New JSP File** modificare il percorso per il nuovo file in *sample/src/main/webapp*. Assegnare quindi al file il nome **login.jsp** e fare clic su **Finish**.

14. Il nuovo file **login.jsp** verrà aperto automaticamente. Sostituire il codice generato automaticamente con il codice seguente e quindi salvare il file:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1"  pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"> 
            <title>Login Page</title>
        </head>
        <body>
            <h3>Login Page</h3>
            <a href="<%=FederatedLoginManager.getFederatedLoginUrl(request.getParameter("returnUrl"))%>"><%=FederatedConfiguration.getInstance().getStsFriendlyName()%></a>
        </body>
        </html> 

15. In **Project Explorer** espandere la cartella **/src/main** del progetto **sample**. Fare clic con il pulsante destro del mouse sulla cartella **resources**, scegliere **New** e quindi fare clic su **Other**.

16. Nella finestra di dialogo **New** espandere la cartella **JBoss Tools Web**, fare clic su **Properties File** e quindi su **Next**.

17. Nella finestra di dialogo **New File Properties** assegnare al file il nome **federation** e quindi fare clic su **Finish**.

18. In **Project Explorer** espandere la cartella **src/main/resources** del progetto **sample**. Fare clic con il pulsante destro del mouse sul file **federation.properties**, scegliere **Open With** e quindi fare clic su **Text Editor**.

19. Nel file **federation.properties** includere le seguenti voci di configurazione e quindi salvare il file:

        federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
        federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
        federation.trustedissuers.friendlyname=Fabrikam
        federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
        federation.realm=spn:7829c758-2bef-43df-a685-717089474505
        federation.reply=https://localhost:8443/sample/wsfed-saml 

    > [WACOM.NOTE] I valori **audienceuris** e **realm** devono essere preceduti da "spn:".

20. A questo punto è necessario creare un nuovo servlet. Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **New**, fare clic su **Other** e quindi su **Servlet**.

21. Nella finestra di dialogo **Create Servlet** specificare un valore **Class name** per *FederationServlet* e fare clic su **Finish**.

22. Il file **FederationServlet.java** verrà aperto automaticamente. Sostituirne il contenuto con il codice seguente e quindi salvare il file:

        import java.io.IOException;
        import javax.servlet.ServletException;
        import javax.servlet.http.HttpServlet;
        import javax.servlet.http.HttpServletRequest;
        import javax.servlet.http.HttpServletResponse;
        import com.microsoft.samples.federation.FederatedAuthenticationListener;
        import com.microsoft.samples.federation.FederatedLoginManager;
        import com.microsoft.samples.federation.FederatedPrincipal;
        import com.microsoft.samples.federation.FederationException;
            
        public class FederationServlet extends HttpServlet {
            private static final long serialVersionUID = 1L;

            protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
                String token = request.getParameter("wresult").toString();

                if (token == null) {
                    response.sendError(400, "You were supposed to send a wresult parameter with a token");
                }
                FederatedLoginManager loginManager = FederatedLoginManager.fromRequest(request, new SampleAuthenticationListener());

                try {
                    loginManager.authenticate(token, response);
                } catch (FederationException e) {
                    response.sendError(500, "Oops! and error occurred.");
                }
            }

            private class SampleAuthenticationListener implements FederatedAuthenticationListener {
                @Override
                public void OnAuthenticationSucceed(FederatedPrincipal principal) {
                    // ***
                    // do whatever you want with the principal object that contains the token's claims
                    // ***
                }
            }
        } 

23. In **Project Explorer** espandere la cartella **src/main/webapp/WEB-INF**. Fare clic con il pulsante destro del mouse sul file **web.xml**, scegliere **Open With** e quindi fare clic su **Text Editor**.

24. Nel file **web.xml** sostituire l'impostazione di **/FederationServlet** nella sezione *&lt;url-pattern\>* con **/ws-saml**. Ad esempio:

        <servlet>
            <description></description>
            <display-name>FederationServlet</display-name>
            <servlet-name>FederationServlet</servlet-name>
            <servlet-class>FederationServlet</servlet-class>
        </servlet>
        <servlet-mapping>
            <servlet-name>FederationServlet</servlet-name>
            <url-pattern>/wsfed-saml</url-pattern>
        </servlet-mapping> 

25. In **Project Explorer** espandere la cartella **src/main/webapp**. Fare clic con il pulsante destro del mouse sul file **index.jsp**, scegliere **Open With** e quindi fare clic su **Text Editor**.

26. Nel file **index.jsp** sostituire il codice esistente con quello riportato di seguito e quindi salvare il file:

        <%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
        <%@ page import="com.microsoft.samples.federation.*"%>
        <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
        <html>
        <head>
            <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
            <title>Index Page</title>
        </head>
        <body>
            <h3>Welcome <strong><%=FederatedLoginManager.fromRequest(request).getPrincipal().getName()%></strong>!</h3>
            <h2>Claim list:</h2>
            <ul> 
                <% for (Claim claim : FederatedLoginManager.fromRequest(request).getClaims()) { %>
                <li><%= claim.toString()%></li>
                <%  } %>
            </ul>
        </body>
        </html> 

27. In **Project Explorer** espandere la cartella **src/main/webapp/WEB-INF**. Fare clic con il pulsante destro del mouse sul file **web.xml**, scegliere **Open With** e quindi fare clic su **Text Editor**.

28. È ora possibile abilitare SSL per l'applicazione. Nel file **web.xml** inserire la sezione *&lt;security-constraint\>* seguente nella sezione *&lt;web-app\>* e quindi salvare il file:

        <security-constraint>
            <web-resource-collection>
                <web-resource-name>SSL Forwarding</web-resource-name>
                <url-pattern>/*</url-pattern>
                <http-method>POST</http-method>
                <http-method>GET</http-method>
            </web-resource-collection>
            <user-data-constraint>
                <transport-guarantee>CONFIDENTIAL</transport-guarantee>
            </user-data-constraint>
        </security-constraint> 

    > [WACOM.NOTE] Prima di procedere, assicurarsi che JBoss Server sia già configurato per supportare SSL.

29. A questo punto è possibile eseguire l'applicazione end-to-end. Fare clic con il pulsante destro del mouse sul progetto **sample**, scegliere **Run As** e quindi fare clic su **Run on Server**. Accettare i valori specificati in precedenza e fare clic su **Finish**.

30. La pagina di accesso verrà aperta nel browser JBoss. Se si fa clic sul collegamento **Awesome Computers**, si verrà reindirizzati alla pagina del provider di identità Office 365, dove è possibile effettuare l'accesso utilizzando le credenziali di tenant della directory, ad esempio *john.doe@fabrikam.onmicrosoft.com*.

31. Dopo aver effettuato l'accesso, si verrà reindirizzati nuovamente alla pagina protetta **sample/index.jsp** come utente autenticato.

Riepilogo
---------

In questa esercitazione è stato illustrato come creare e configurare un'applicazione Java a singolo tenant che utilizza le funzionalità Single Sign-On di Azure Active Directory.

