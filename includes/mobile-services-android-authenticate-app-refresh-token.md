La cache di token dovrebbe funzionare in una situazione semplice, ma cosa succede alla scadenza o alla revoca del token? È necessario essere in grado di rilevare i token scaduti. È possibile che il token scada quando l'app non è in esecuzione. La cache del token non sarebbe quindi valida. È anche possibile che la scadenza del token si verifichi mentre l'app è effettivamente in esecuzione durante una chiamata effettuata direttamente dall'app o una chiamata effettuata dalla libreria di Servizi mobili. In questo caso si otterrà un codice di stato HTTP 401 "Non autorizzato". È quindi necessario potere rilevare questa situazione e aggiornare il token. Si usa quindi un [ServiceFilter][] dalla [libreria del client Android][].

In questa sezione sarà definito un ServiceFilter che rileverà una risposta di tipo codice di stato HTTP 401 e attiverà un aggiornamento del token e della cache di token. ServiceFilter bloccherà anche altre richieste in uscita durante l'autenticazione, in modo che queste richieste possano usare il token aggiornato.

1.  In Eclipse aprire il file ToDoActivity.java e aggiungere le istruzioni import seguenti:

        import java.util.concurrent.atomic.AtomicBoolean;

2.  Nel file ToDoActivity.java aggiungere i membri seguenti alla classe ToDoActivity.

        public boolean bAuthenticating = false;
        public final Object mAuthenticationLock = new Object();

    Saranno usati per semplificare la sincronizzazione dell'autenticazione dell'utente, in modo che sia eseguita una sola volta. Eventuali chiamate effettuate durante l'autenticazione devono rimanere in attesa e usare il nuovo token dall'autenticazione in corso.

3.  Nel file ToDoActivity.java aggiungere il metodo seguente alla classe ToDoActivity, che sarà usata per bloccare le chiamate in uscita in altri thread durante l'esecuzione dell'autenticazione.

        /**
         * Detects if authentication is in progress and waits for it to complete. 
         * Returns true if authentication was detected as in progress. False otherwise.
         */
        public boolean detectAndWaitForAuthentication()
        {
            boolean detected = false;
            synchronized(mAuthenticationLock)
            {
                do
                {
                    if (bAuthenticating == true)
                        detected = true;
                    try
                    {
                        mAuthenticationLock.wait(1000);
                    }
                    catch(InterruptedException e)
                    {}
                }
                while(bAuthenticating == true);
            }
            if (bAuthenticating == true)
                return true;

            return detected;
        }

4.  Nel file ToDoActivity.java aggiungere il metodo seguente alla classe ToDoActivity. Questo metodo attiverà effettivamente l'attesa e quindi aggiornerà il token nelle richieste in uscita al termine dell'autenticazione.

        /**
         * Waits for authentication to complete then adds or updates the token 
         * in the X-ZUMO-AUTH request header.
         * 
         * @param request
         *            The request that receives the updated token.
         */
        private void waitAndUpdateRequestToken(ServiceFilterRequest request)
        {
            MobileServiceUser user = null;
            if (detectAndWaitForAuthentication())
            {
                user = mClient.getCurrentUser();
                if (user != null)
                {
                    request.removeHeader("X-ZUMO-AUTH");
                    request.addHeader("X-ZUMO-AUTH", user.getAuthenticationToken());
                }
            }
        }

5.  Nel file ToDoActivity.java aggiornare il metodo `authenticate` della classe ToDoActivity, in modo che accetti un parametro booleano per permettere l'imposizione dell'aggiornamento dei token e della cache di token. È anche necessario inviare una notifica a eventuali thread bloccati al termine dell'autenticazione, in modo che possano usare il nuovo token.

        /**
         * Authenticates with the desired login provider. Also caches the token. 
         * 
         * If a local token cache is detected, the token cache is used instead of an actual 
         * login unless bRefresh is set to true forcing a refresh.
         * 
         * @param bRefreshCache
         *            Indicates whether to force a token refresh. 
         */
        private void authenticate(boolean bRefreshCache) {

            bAuthenticating = true;

            if (bRefreshCache || !loadUserTokenCache(mClient))
            {
                // New login using the provider and update the token cache.
                mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                        new UserAuthenticationCallback() {
                            @Override
                            public void onCompleted(MobileServiceUser user,
                                    Exception exception, ServiceFilterResponse response) {

                                synchronized(mAuthenticationLock)
                                {
                                    if (exception == null) {
                                        cacheUserToken(mClient.getCurrentUser());
                                        createTable();
                                    } else {
                                        createAndShowDialog(exception.getMessage(), "Login Error");
                                    }
                                    bAuthenticating = false;
                                    mAuthenticationLock.notifyAll();
                                }
                            }
                        });
            }
            else
            {
                // Other threads may be blocked waiting to be notified when 
                // authentication is complete.
                synchronized(mAuthenticationLock)
                {
                    bAuthenticating = false;
                    mAuthenticationLock.notifyAll();
                }
                createTable();
            }
        }   

6.  Nel file ToDoActivity.java aggiungere la definizione seguente per la classe `RefreshTokenCacheFilter` nella classe ToDoActivity:

        /**
         * The RefreshTokenCacheFilter class filters responses for HTTP status code 401. 
         * When 401 is encountered, the filter calls the authenticate method on the 
         * UI thread. Out going requests and retries are blocked during authentication. 
         * Once authentication is complete, the token cache is updated and 
         * any blocked request will receive the X-ZUMO-AUTH header added or updated to 
         * that request.   
         */
        private class RefreshTokenCacheFilter implements ServiceFilter {

            AtomicBoolean mAtomicAuthenticatingFlag = new AtomicBoolean();

            /**
             * The AuthenticationRetryFilterCallback class is a wrapper around the response 
             * callback that encapsulates the request and other information needed to enable 
             * a retry of the request when HTTP status code 401 is encountered. 
             */
            private class AuthenticationRetryFilterCallback implements ServiceFilterResponseCallback
            {
                // Data members used to retry the request during the response.
                ServiceFilterRequest mRequest;
                NextServiceFilterCallback mNextServiceFilterCallback;
                ServiceFilterResponseCallback mResponseCallback;

                public AuthenticationRetryFilterCallback(ServiceFilterRequest request, 
                        NextServiceFilterCallback nextServiceFilterCallback, 
                        ServiceFilterResponseCallback responseCallback)
                {
                    mRequest = request;
                    mNextServiceFilterCallback = nextServiceFilterCallback;
                    mResponseCallback = responseCallback;
                }

                @Override
                public void onResponse(ServiceFilterResponse response, Exception exception) {

                    // Filter out the 401 responses to update the token cache and 
                    // retry the request
                    if ((response != null) && (response.getStatus().getStatusCode() == 401))
                    { 
                        // Two simultaneous requests from independent threads could get HTTP 
                        // status 401. Protecting against that right here so multiple 
                        // authentication requests are not setup to run on the UI thread.
                        // We only want to authenticate once. Other requests should just wait 
                        // and retry with the new token.
                        if (mAtomicAuthenticatingFlag.compareAndSet(false, true))                           
                        {
                            // Authenticate on UI thread
                            runOnUiThread(new Runnable() {
                                @Override
                                public void run() {
                                    // Force a token refresh during authentication.
                                    authenticate(true);
                                }
                            });
                        }

                        // Wait for authentication to complete then 
                        // update the token in the request.
                        waitAndUpdateRequestToken(this.mRequest);
                        mAtomicAuthenticatingFlag.set(false);                       

                        // Retry recursively with a new token as long as we get a 401.
                        mNextServiceFilterCallback.onNext(this.mRequest, this);
                    }

                    // Responses that do not have 401 status codes just pass through.
                    else if (this.mResponseCallback != null)  
                      mResponseCallback.onResponse(response, exception);
                }
            }


            @Override
            public void handleRequest(final ServiceFilterRequest request, 
                final NextServiceFilterCallback nextServiceFilterCallback,
                ServiceFilterResponseCallback responseCallback) {

                // In this example, if authentication is already in progress we block the request
                // until authentication is complete to avoid unnecessary authentications as 
                // a result of HTTP status code 401. 
                // If authentication was detected, add the token to the request.
                waitAndUpdateRequestToken(request);

                // Wrap the request in a callback object that will facilitate retries.
                AuthenticationRetryFilterCallback retryCallbackObject = 
                    new AuthenticationRetryFilterCallback(request, nextServiceFilterCallback,
                          responseCallback); 

                // Send the request down the filter chain.
                nextServiceFilterCallback.onNext(request, retryCallbackObject);         
            }
        }

    Questo filtro del servizio controllerà ogni risposta, alla ricerca del codice di stato HTTP 401 "Non autorizzato". In caso di errore 401, nel thread dell'interfaccia utente sarà configurata una nuova richiesta di accesso per ottenere un nuovo token. Le altre chiamate saranno bloccate fino al completamento dell'accesso. Dopo il recupero del nuovo token, la richiesta che ha attivato il codice di stato 401 sarà ritentata con il nuovo token ed eventuali chiamate bloccate saranno ritentate con il nuovo token.

7.  Nel file ToDoActivity.java aggiornare il metodo `onCreate` come indicato di seguito:

        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);

            setContentView(R.layout.activity_to_do);
            mProgressBar = (ProgressBar) findViewById(R.id.loadingProgressBar);

            // Initialize the progress bar
            mProgressBar.setVisibility(ProgressBar.GONE);

            try {
                // Create the Mobile Service Client instance, using the provided
                // Mobile Service URL and key
                mClient = new MobileServiceClient(
                        "https://<YOUR MOBILE SERVICE>.azure-mobile.net/",
                        "<YOUR MOBILE SERVICE KEY>", this)
                           .withFilter(new ProgressFilter())
                           .withFilter(new RefreshTokenCacheFilter());

                // Authenticate passing false to load the current token cache if available.
                authenticate(false);

            } catch (MalformedURLException e) {
                createAndShowDialog(new Exception("Error creating the Mobile Service. " +
                    "Verify the URL"), "Error");
            }
        }

    RefreshTokenCacheFilter è usato in questo codice in aggiunta a ProgressFilter. Durante `onCreate` è anche necessario che sia caricata la cache di token. Sarà quindi passato false nel metodo `authenticate`.

  [ServiceFilter]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/ServiceFilter.html
  [libreria del client Android]: http://dl.windowsazure.com/androiddocs/
