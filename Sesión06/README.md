# Sesión 6 # Postwork: 

## :dart: Objetivos

- Adaptar scripts de pruebas automatizados para que pueden ser ejecutados  múltiples navegadores (cross browser testing).
- Construir nuevos scripts de pruebas automatizados donde se implemente la ejecución en máquinas remotas con Selenium Grid.


## ⚙ Requisitos

- Software de desarrollo
    - Java Software Development Kit (JDK)

- Editor de código
    - Eclipse IDE

- Entorno de pruebas de software
    - Selenium Java Client
    - Selenium Webdriver
    - Selenium Server GRID

- Navegador
    - Google Chrome

- Selenium browserdriver
    - Chromium/Chrome
    - Mozilla (geckodriver)

## Instrucciones

- Adaptar scripts de pruebas automatizados para que pueden ser ejecutados  múltiples navegadores (cross browser testing).
    - Selecciona una funcionalidad de mercado libre para automatizar o una que ya tengas automatizada. 
        + `Pro-Tip:` es recomendable automatizar una nueva funcionalidad, de esta manera puedes tener un nuevo mecanismo de ejecución de pruebas en tu proyecto.

    - Agrega la parametría al archivo `testng.xml` para que tome la información de los navegadores (2 como mínimo). 
        + `Pro-tip:` Puedes incluir más información en este archivo sobre el navegador que necesites posteriormente en la clase de prueba, como por ejemplo: nombre del explorador, versión, la ruta donde se encuentra el driver, etc.
        
        + `Buena Práctica:` como no tenemos forma de acceder a google analytics o alguna herramienta que nos de la información de cuáles son los navegadores con los que más se ingresa a la web de mercado libre, puedes investigar en internet cuales son los exploradores más utilizados generalmente, eso te ayudará a saber cuáles priorizar.

    - Utiliza la anotación `@Parameters()` en clase de prueba para que consuma la parametría del archivo `testng.xml`
    - Adapta los métodos de pruebas para que ejecuten las pruebas depende de los navegadores parametrizados.

- Construir nuevos scripts de pruebas automatizados donde se implemente la ejecución en máquinas remotas con Selenium Grid 4.
    - Selecciona una funcionalidad de mercado libre para automatizar o una que ya tengas automatizada. 
        + `Pro-Tip:` es recomendable automatizar una nueva funcionalidad, de esta manera puedes tener un nuevo mecanismo de ejecución de pruebas en tu proyecto.
    - Crea la clase de prueba donde se utilicen los objetos DesiredCapabilites y RemoteWebDriver.
    - Inicia Selenium Grid 4
        + `Pro-Tip:` recomendamos usar el rol standalone de Selenium Grid 4 (donde el mismo servidor es el hub y el node). 
    - Ejecuta las pruebas automatizadas.

## Desarrollo
- Creación del archivo testng.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="TestSuite">
    <test name="ChromeTest">
        <parameter name="browser" value="Chrome" />
        <parameter name="driver_name" value="webdriver.chrome.driver" />
        <parameter name="driver_path" value="chromedriver" />
        <classes>
            <class name="Tests.sesion06_Test01"></class>
        </classes>
    </test>

    <test name="FirefoxTest">
        <parameter name="browser" value="Firefox" />
        <parameter name="driver_name" value="webdriver.gecko.driver" />
        <parameter name="driver_path" value="geckodriver" />
        <classes>
            <class name="Tests.sesion06_Test01"></class>
        </classes>
    </test>
    <test name="MicrosoftEdgeTest">
        <parameter name="browser" value="MicrosoftEdge" />
        <parameter name="driver_name" value="webdriver.msedge.driver" />
        <parameter name="driver_path" value="msedgedriver" />
        <classes>
            <class name="Tests.sesion06_Test01"></class>
        </classes>
    </test>
</suite>
```
- Importación de tres drivers dentro del proyecto

![image](https://user-images.githubusercontent.com/77414220/169712105-33708215-eee7-4ead-b2f5-aee694805179.png)

- Adaptación de pruebas para navegadores parametrizados
```java
public class sesion06_Test01 {

    WebDriver driver;

    homePage HomePage;
    createUserPage CreateUserPage;
    createUserConfirmationPage CreateUserConfirmationPage;

    @BeforeClass
    @Parameters({"driver_name", "driver_path"})
    public void setUpAll(String driver_name, String driver_path) {
        System.setProperty(driver_name, driver_path);
    }

    @BeforeTest
    @Parameters({"browser"})
    public void setUp(String browser) throws Exception {
        if(browser.equalsIgnoreCase("Firefox")) {
            driver = new FirefoxDriver();
        } else if (browser.equalsIgnoreCase("Chrome")) {
            driver = new ChromeDriver();
        } else if (browser.equalsIgnoreCase("MicrosoftEdge")) {
            driver = new EdgeDriver();
        } else {
            //lanzamos una exepción
            throw new Exception("Navegador no parametrizado...");
        }
        driver.manage().window().maximize();
        driver.get("https://www.mercadolibre.com.mx/");
        HomePage = new homePage(driver);
    }

    @AfterTest
    public void afterEach() {
        driver.close();
    }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "MySQL_dataprovider")
    public void createAccount(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "dataProvider2")
    public void createAccount1(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }
}
```
- Adaptación de pruebas para navegadores en máquinas remotas con Selenium Grid.
```java
public class sesion06_Test02 {
    WebDriver driver;

    homePage HomePage;
    createUserPage CreateUserPage;
    createUserConfirmationPage CreateUserConfirmationPage;

    private final String nodeUrl = "http://localhost:4444/";

    @BeforeTest
    public void setUp() throws Exception {
        System.setProperty("webdriver.msedge.driver", "msedgedriver.exe");
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName("MicrosoftEdge");
        capabilities.setPlatform(Platform.WIN10);
        driver = new RemoteWebDriver(new URL(nodeUrl), capabilities);
        driver.manage().window().maximize();
        driver.get("https://www.mercadolibre.com.mx/");
        HomePage = new homePage(driver);
    }

    @AfterTest
    public void afterEach() {
            driver.close();
        }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "MySQL_dataprovider")
    public void createAccount(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }

    @Test(dataProviderClass = MyDataProvider.class, dataProvider = "dataProvider2")
    public void createAccount1(String name, String lastName, String email, String password) throws InterruptedException {
        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        if(CreateUserPage.isCreateDisplayed()) {
            CreateUserPage.fillName(name);
            CreateUserPage.fillLastName(lastName);
            CreateUserPage.fillEmail(email);
            CreateUserPage.fillPassword(password);
            CreateUserPage.clickCheckbox();
            CreateUserPage.clickCreate();
        }

        Thread.sleep(2000);

        String actualOutput;
        String expectedOutput;

        try {
            actualOutput = CreateUserPage.getAuthInputText();
            expectedOutput = "Marca la casilla de verificación";
        }catch(Exception e){
            CreateUserConfirmationPage = CreateUserPage.verificationCodePage();
            actualOutput = CreateUserConfirmationPage.codeConfirmationText();
            expectedOutput = "Ingresa el código que te enviamos por e-mail";
        }

        assertEquals(expectedOutput,actualOutput);
    }
}
```
