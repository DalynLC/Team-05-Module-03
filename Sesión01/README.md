![Cognizant_logo_2022 svg](https://user-images.githubusercontent.com/77414220/167276034-fc9aba50-8b81-4ce6-8da8-db3aea61e87b.png)

## INTEGRANTES DEL EQUIPO

- Osmar Barraza Flores
- Cruz Gil Estrada
- Dalyn Lara Cortes
- Carlos Valdivia Garcia

## :dart: Objetivos

- Creación de casos de prueba para la página web de mercadolibre: https://www.mercadolibre.com.mx/
- Configurar y ejecutar nuestro primer script en Eclipse IDE  con TesngNG + Selenium.


## ⚙ Requisitos

+ Eclipse IDE
+ Configuración del browser driver: Chromium/Chrome
+ Dependencias maven en archivo pom.xml
+ Configuración de TestNG



## Instrucciones
- Seleccionar alguna funcionalidad de la página web de mercadolibre en la que desee automatizar sus casos de prueba, y generar un archivo de casos de pruebas con al menos 10 casos de prueba.
- Crear proyecto maven en Eclipse IDE.
- Integrar el browser driver de Chrome en el proyecto.
- Configurar TestNG en el proyecto.
- Ejecutar un script de prueba donde:
  - Se abre el navegador (Google Chrome) maximizado.
  - Se despliegue el explorador en la pagina de mercadolibre: https://www.mercadolibre.com.mx/ 
  - Se cierre el explorador

## Desarrollo
- Selección de funcionalidades a probar:
  - Búsqueda de productos
  - Selección de la opción ayuda.
  - Selección de la opción Terminos y Condiciones
  - Creación de un usuario
  - Seleccionar un item de los mostrados en la búsqueda.
  
 - Configuración del proyecto (en Gradle)
 ```java
     plugins {
        id 'java'
    }

    group 'org.example'
    version '1.0-SNAPSHOT'

    compileJava.options.encoding = 'UTF-8'

    tasks.withType(JavaCompile) {
        options.encoding = 'UTF-8'
    }
    repositories {
        mavenCentral()
    }


    dependencies {
        testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.0'
        testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.0'
        testImplementation 'org.seleniumhq.selenium:selenium-java:4.1.4'
        testImplementation 'org.testng:testng:7.4.0'
    }

    test {
        useTestNG()
        //useJUnitPlatform()
    }
 ```
- Integración del browser driver de Chrome en el proyecto.
  
  ![image](https://user-images.githubusercontent.com/77414220/169681994-c24c4583-aa79-4f55-96b7-4fd24cb5d81a.png)

- Configuración de TestNG en el proyecto y desarrollo de pruebas.
```java
    import org.openqa.selenium.By;
    import org.openqa.selenium.WebDriver;
    import org.openqa.selenium.WebElement;
    import org.openqa.selenium.chrome.ChromeDriver;

    import org.testng.Assert;
    import org.testng.annotations.*;

    import java.time.Duration;

    import static org.testng.AssertJUnit.assertEquals;

    public class sesion01_Test01 {

        WebDriver driver;

        @BeforeTest
        public void setUpAll(){
            System.setProperty("webdriver.chrome.driver", "chromedriver.exe");
            driver = new ChromeDriver();
        }

        @BeforeMethod
        public void setUpEach(){
            driver.manage().window().maximize();
            driver.get("https://www.mercadolibre.com.mx");
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        }

        @AfterTest
        public void close(){
            driver.close();
        }


        //1
        @Test
        public void paginaInicio(){

            String actualTitle = driver.getTitle();
            String expectedTitle = "Mercado Libre México - Envíos Gratis en el día";

            assertEquals(expectedTitle,actualTitle);
        }


        //2
        @Test
        public void busquedaAleatoria(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("balon");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Balon";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

        //3
        @Test
        public void verifySearchTittle(){
            driver.findElement(By.name("as_word")).sendKeys("Test");
            driver.findElement(By.name("as_word")).submit();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Test";
            assertEquals(expectedTitle,actualTitle);
            System.out.println("Priority 3");
        }

        //4
        @Test
        public void comprobacionContenido(){

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
        @Test
        public  void seleccionarItem(){

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


        }

        //6. Click en ayuda
        @Test
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

        }

        //7. Crear cuenta
        @Test
        public void crearUsuario() throws InterruptedException {

            WebElement crear = driver.findElement(By.xpath("//*[@id=\"nav-header-menu\"]//a[1]"));
            crear.click();
            try {
                driver.findElement(By.className("cookie-consent-banner-opt-out__action--key-accept")).click();
                driver.findElement(By.className("cookie-consent-snackbar__close")).click();
            }catch(Exception e){
                System.out.println("Cookies banner not displayed");
            }
            Thread.sleep(2000);

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

        }

        //Test 8
        @Test
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
        }
        //9
        @Test
        public void busquedaLibros(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Libros");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Libros";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

        //10
        @Test
        public void busquedaMousepad(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Mousepad");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Mousepad";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

        //10
        @Test
        public void busquedaTeclado(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Teclado");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Teclado";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

        //11
        @Test
        public void busquedaLaptop(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Laptop");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Laptop";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

        //12
        @Test
        public void busquedaAudifonos(){

            WebElement search = driver.findElement(By.cssSelector("#cb1-edit"));
            WebElement searchIcon = driver.findElement(By.cssSelector("div[aria-label='Buscar']"));
            search.sendKeys("Audifonos");
            searchIcon.click();

            String actualTitle = driver.findElement(By.xpath("//*[@id=\"root-app\"]//div//div[1]//aside//div[1]//h1")).getText();
            String expectedTitle = "Audifonos";

            System.out.println(actualTitle);

            assertEquals(expectedTitle,actualTitle);

        }

    }
```
- Ejecución de las pruebas.
