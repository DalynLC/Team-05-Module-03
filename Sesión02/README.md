# Sesión #2 Postwork: 

## :dart: Objetivos

- Automatizar los casos de prueba elaborados en el postwork de la sesión 1 haciendo uso de localizadores, técnicas de manejo de errores, de las clases WebDriver/WebElement y finalmente usando afirmaciones.
- Emplear el uso de las anotaciones de TestNG en los scripts de pruebas automatizados.
- Reconocer la necesidad de implementar esperas (waits) en los script de pruebas.



## ⚙ Requisitos

- Software de desarrollo
    - Java Software Development Kit (JDK)
- Editor de código
    - Eclipse IDE
- Entorno de pruebas de software
    - Selenium Java Client
    - Selenium Webdriver
    - Selenium IDE
- Navegador
    - Google Chrome
- Selenium browserdriver
    - Chromium/Chrome


## Instrucciones
- Estructura los casos de pruebas en tu proyecto usando las siguientes anotaciones TestNG para darles lógica de ejecución:
    - @BeforeTest
    - @BeforeMethod
    - @Test
    - @AfterMethod
    - @AfterTest

- Agregale prioridad a tus casos de pruebas con el uso de la anotación de TestNG `@Test (Priority)`
- Utiliza selenium IDE o la herramienta de desarrolladores de google chrome para identificar los localizadores de los elementos en la pantalla para luego incluirlos es los scripts de prueba. __Pro-tip:__ Asegúrate que si tiene id o name usa preferiblemente estos localizadores.
- Incluye en tu código tipos de esperas explícitas, incluyendo a su vez las condiciones esperadas.
- Trata de incluir técnicas de manejo de errores como `Try/catch`.
- Utiliza propiedades/métodos de la clase WebDriver como: `driver.close()`, `driver.manage()`, `driver.get()`.
- Utiliza propiedades/métodos de la clase WebElement como: `click()`, `sendKeys()`,`clear()`.
- Incluye aserciones en tu código para validar el resultado esperado del caso de prueba que estás ejecutando. __Pro-tip:__ Si lo requieres puedes incluir varias aserciones en un mismo caso de prueba. 

## Desarrollo
- Reconstrucción del código implementando las nuevas especificaciones.
    ```java
    public class sesion02_Test01 {
        WebDriver driver;

        @BeforeTest
        public void setUpAll(){

            System.out.println("Starting Test set");
            System.setProperty("webdriver.chrome.driver", "chromedriver.exe");
            driver = new ChromeDriver();

        }

        @BeforeMethod
        public void setUpEachTime(){

            driver.manage().window().maximize();
            driver.get("https://www.mercadolibre.com.mx");
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        }

        @AfterMethod
        public void finishTest(){
            System.out.println("Ending Test set");
        }

        @AfterTest
        public void close(){
            driver.close();
        }


        //1
        @Test(priority = 0)
        public void paginaInicio() {
            try {
                Boolean firstResult = new WebDriverWait(driver, Duration.ofSeconds(5)).until(ExpectedConditions.titleIs("Mercado Libre México - Envíos Gratis en el día"));
                System.out.println("Priority 0");
                assertTrue(firstResult);
            } catch (Exception e) {
                System.out.println(e);
            }
        }

        //2
        @Test(priority = 3)
        public void busquedaAleatoria(){
            WebElement firstResult = new WebDriverWait(driver, Duration.ofSeconds(10))
                    .until(ExpectedConditions.elementToBeClickable(By.cssSelector("div[aria-label='Buscar']")));
            try{
                WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
                WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
                search.sendKeys("balon");
                searchIcon.click();

                String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
                String expectedTitle = "Balon";

                System.out.println(actualTitle);

                assertEquals(expectedTitle,actualTitle);
            }catch (Exception e){
                System.out.println(e);
            }

        }

        //3
        @Test(priority = 2)
        public void verifySearchTittle(){
            driver.findElement(By.name("as_word")).sendKeys("Test");
            driver.findElement(By.name("as_word")).submit();
            assertEquals("Test | MercadoLibre \uD83D\uDCE6",driver.getTitle());
            System.out.println("Priority 2");
        }

        //4
        @Test (priority = 2)
        public void comprobacionContenido(){

            WebElement firstResult = new WebDriverWait(driver, Duration.ofSeconds(10))
                    .until(ExpectedConditions.elementToBeClickable(By.cssSelector("div[aria-label='Buscar']")));

            WebElement search = driver.findElement(By.cssSelector ("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("nintendo");
            searchIcon.click();

            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
            WebElement result1 = driver.findElement(By.xpath("(//h2[@class='ui-search-item__title'][contains(text(),'Nintendo Switch Lite 32GB Standard color')])[1]"));
            String resultado1 = result1.getText();
            Assert.assertTrue(resultado1.contains("Nintendo"));
            System.out.println("Priority 2");


        }

        //5
        @Test (priority = 2)
        public  void seleccionarItem(){

            WebElement firstResult = new WebDriverWait(driver, Duration.ofSeconds(10))
                    .until(ExpectedConditions.elementToBeClickable(By.cssSelector("div[aria-label='Buscar']")));

            WebElement search = driver.findElement(By.cssSelector ("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("pantalon");
            searchIcon.click();
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

            driver.findElement(By.cssSelector("body > main:nth-child(3) > div:nth-child(1) > div:nth-child(3) > section:nth-child(2) > ol:nth-child(7) > li:nth-child(1) > div:nth-child(1) > div:nth-child(1)")).click();
            String title = driver.findElement(By.cssSelector(".ui-pdp-title")).getText();
            System.out.println(title);

            Assert.assertTrue(title.contains("Pantalón"));
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

            System.out.println("Priority 2");
        }

        //6. Click en ayuda
        @Test(priority = 1)
        public void opcionAyuda(){
            try {
                driver.findElement(By.className("cookie-consent-banner-opt-out__action--key-accept")).click();
                driver.findElement(By.className("cookie-consent-snackbar__close")).click();
            }catch(Exception e){
                System.out.println("Cookies banner not displayed");
            }
            WebElement search = driver.findElement(By.xpath("//html//body//header//div//div[2]//ul//li[8]//a"));
            search.click();

            String actualOutput = driver.findElement(By.className("search-bar__subtitle")).getText();
            String expectedOutput = "¿Con qué podemos ayudarte?";

            System.out.println(actualOutput);

            assertEquals(expectedOutput,actualOutput);
            System.out.println("Priority 1");

        }

        //7. Crear cuenta
        @Test (priority = 0)
        public void crearUsuario() throws InterruptedException {

            WebElement crear = driver.findElement(By.xpath("//*[@id=\"nav-header-menu\"]//a[1]"));
            crear.click();
            try {
                driver.findElement(By.className("cookie-consent-banner-opt-out__action--key-accept")).click();
                driver.findElement(By.className("cookie-consent-snackbar__close")).click();
            }catch(Exception e){
                System.out.println("Cookies banner not displayed");
            }

            WebElement firstResult = new WebDriverWait(driver, Duration.ofSeconds(10))
                    .until(ExpectedConditions.elementToBeClickable(By.className("andes-button__content")));


            driver.findElement(By.id("firstName")).sendKeys("Anita");
            driver.findElement(By.id("lastName")).sendKeys("perez");
            driver.findElement(By.id("email")).sendKeys("anitaPerez@invalidmymail.com");
            driver.findElement(By.id("password")).sendKeys("AnitaPz!123");
            driver.findElement(By.id("tyc_checkbox")).click();
            driver.findElement(By.className("andes-button__content")).click();

            Thread.sleep(2000);

            String actualOutput;
            String expectedOutput;

            try {
                actualOutput = driver.findElement(By.className("auth-input-error")).getText();
                expectedOutput = "Marca la casilla de verificación";
            }catch(Exception e){
                actualOutput = driver.findElement(By.className("enter-code-content__title")).getText();
                expectedOutput = "Ingresa el código que te enviamos por e-mail";
            }

            assertEquals(expectedOutput,actualOutput);
            System.out.println("Priority 0");

        }

        //Test 8
        @Test (priority = 1)
        public void terminosCondiciones(){
            try {
                driver.findElement(By.className("cookie-consent-banner-opt-out__action--key-accept")).click();
                driver.findElement(By.className("cookie-consent-snackbar__close")).click();
            }catch(Exception e){
                System.out.println("Cookies banner not displayed");
            }
            WebElement terms = driver.findElement(By.xpath("//html//body//footer//div//div[1]//div//nav//a[2]"));
            terms.click();

            String actualOutput = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[3]//div//div[2]//div[2]//div//div//h2[2]")).getText();
            String expectedOutput = "2- Términos y Condiciones";
            assertEquals(expectedOutput,actualOutput);
            System.out.println("Priority 1");
        }
        //9
        @Test (priority = 2)
        public void busquedaLibros(){
            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            search.clear();
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Libro");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Libro";

            System.out.println(actualTitle);
            assertEquals(expectedTitle,actualTitle);
            System.out.println("Priority 3");
        }

        //10
        @Test (priority = 2)
        public void busquedaMousepad(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            search.clear();
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Mousepad");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Mousepad";

            System.out.println(actualTitle);
            assertEquals(expectedTitle,actualTitle);
            System.out.println("Priority 2");

        }

        //11
        @Test(priority = 2)
        public void busquedaTeclado(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            search.clear();
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Teclado");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Teclado";

            System.out.println(actualTitle);
            assertEquals(expectedTitle,actualTitle);
            System.out.println("Priority 2");
        }

        //12
        @Test (priority = 2)
        public void busquedaLaptop(){


            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            search.clear();
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Laptop");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Laptop";

            System.out.println(actualTitle);
            assertEquals(expectedTitle,actualTitle);
            System.out.println("Prioridad 2");

        }

    }
```

- Ejecución de las pruebas.
