# Sesión 3 - Postwork

## :dart: Objetivos

- Implementar Page Object Model (POM) como patrón de diseño en el proyecto de automatización web.
- Construir mediante un diseño conceptual cómo estarán conectadas las clases page object con la clase de prueba.

## ⚙ Requisitos

- __Software de desarrollo__
    - Java Software Development Kit (JDK)
- __Editor de código__
    - Eclipse IDE
- __Entorno de pruebas de software__
    - Selenium Java Client
    - Selenium Webdriver
    - Selenium IDE
- __Navegador__
    - Google Chrome
- __Selenium browserdriver__
    - Chromium/Chrome


## Instrucciones

- Modificar el proyecto de automatización en el que estás trabajando para adaptarlo al modelo POM, creando las carpetas en el proyecto en pro de organizar las clases según la estructura del patrón page object model (POM). 

    <img src="./clases.png" width="40%"> 

- Crear el diagrama de clases para documentar cómo estarán conectadas las clases page object con la clase de prueba. `Pro-tip:` ten en cuenta que el diagramas en este módulo no será evaluado, por el contrario se usará como una guía para conocer como funcionara la comunicación entre las clases.

    <img src="./pom.png" width="40%"> 

- Crear las clases correspondientes al patrón POM por cada página identificada en la funcionalidad a testear, debes como mínimo incluir 3 páginas de la web de mercado libre, puede ser de una misma funcionalidad o varias funcionalidades.
- Crear las clases donde se alojarán los casos de prueba. 
- Intenta crear métodos que realicen varias acciones con los webElements, como que que vimos el el prework:

```Java
public String getMessageText() {
    return driver.findElement(messageBy).getText();
  }
```
> :stop_sign: __Cuidado:__ ten en cuenta las reglas para crear POM al momento de crear las Page Object.

## Desarrollo
- Creación de las carpetas para mejor organización.

![image](https://user-images.githubusercontent.com/77414220/169682605-7f0fe959-e26b-431b-aa08-ac3e0f2b187f.png)

-Creación del diagrama de clases

![S3](https://user-images.githubusercontent.com/77414220/169710642-3ea92257-74b3-40eb-8783-4a1cd5f40fb5.png)

-Creación de las clases para adaptar los test al modelo POM
```java
public class homePage {

    protected WebDriver driver;
    private By searchTexBox = By.cssSelector("#cb1-edit");
    private By searchButton = By.cssSelector("div[aria-label='Buscar']");
    private By helpButton = By.xpath("//html//body//header//div//div[2]//ul//li[8]//a");
    private By termsConditionButton = By.xpath("//html//body//footer//div//div[1]//div//nav//a[2]");
    private By createUserButton = By.xpath("//*[@id=\"nav-header-menu\"]//a[1]");
    private By ofertas = By.xpath("//a[@href='https://www.mercadolibre.com.mx/ofertas#nav-header']");

    protected By cookiesBanner = By.className("cookie-consent-banner-opt-out__action--key-accept");
    protected By cookiesSnackBar = By.className("cookie-consent-snackbar__close");

    public homePage(WebDriver driver) {
        this.driver = driver;
        if(!driver.getCurrentUrl().equals("https://www.mercadolibre.com.mx/")) {
            throw new IllegalStateException("You are not in the homepage");
        }
    }

    public String getPageTitle(){
        return driver.getTitle();
    }

    public void fillSearchText(String text) {
        driver.findElement(searchTexBox).clear();
        driver.findElement(searchTexBox).sendKeys(text);
    }

    public boolean isSearchDisplayed(){
        return driver.findElement(searchButton).isDisplayed();
    }

    public searchPage searchProd(String text){
        fillSearchText(text);
        driver.findElement(searchButton).click();
        return new searchPage(driver);
    }

    public helpPage helpBtn(){
        driver.findElement(helpButton).click();
        return new helpPage(driver);
    }

    public termsConditionsPage tycBtn(){
        driver.findElement(termsConditionButton).click();
        return new termsConditionsPage(driver);
    }

    public createUserPage createUserBtn(){
        driver.findElement(createUserButton).click();
        return new createUserPage(driver);
    }

    public ofertasPage getOfertas(){
        driver.findElement(ofertas).click();
        return new ofertasPage(driver);
    }

    public void closeCookiesBanner(){
        driver.findElement(cookiesBanner).click();
        driver.findElement(cookiesSnackBar).click();
    }

}
```

```java
public class helpPage {

    protected WebDriver driver;
    protected By helpText = By.className("search-bar__subtitle");

    protected By cookiesBanner = By.className("cookie-consent-banner-opt-out__action--key-accept");
    protected By cookiesSnackBar = By.className("cookie-consent-snackbar__close");

    public helpPage(WebDriver driver) {
        this.driver = driver;
    }

    public String getTextComp(By locator){
        return driver.findElement(locator).getText();
    }

    public void clickButton(By locator) {
        driver.findElement(locator).click();
    }

    public String helpTitle(){
        return getTextComp(helpText);
    }

    public void closeCookiesBanner(){
        driver.findElement(cookiesBanner).click();
        driver.findElement(cookiesSnackBar).click();
    }
}
```

```java
public class termsConditionsPage {

    protected WebDriver driver;
    protected By tycText = By.xpath("//*[@id=\"root-app\"]//div//div[3]//div//div[2]//div[2]//div//div//h2[2]");

    protected By cookiesBanner = By.className("cookie-consent-banner-opt-out__action--key-accept");
    protected By cookiesSnackBar = By.className("cookie-consent-snackbar__close");

    public termsConditionsPage(WebDriver driver) {
        this.driver = driver;
    }

    public String getTextComp(By locator){
        return driver.findElement(locator).getText();
    }

    public String tycText(){
        return getTextComp(tycText);
    }

    public void closeCookiesBanner(){
        driver.findElement(cookiesBanner).click();
        driver.findElement(cookiesSnackBar).click();
    }
}
```

```java
public class createUserPage {

    protected WebDriver driver;
    protected By firstName = By.id("firstName");
    protected By lastName = By.id("lastName");
    protected By email = By.id("email");
    protected By password = By.id("password");
    protected By checkbox = By.id("tyc_checkbox");
    protected By createButton = By.className("andes-button__content");

    protected By cookiesBanner = By.className("cookie-consent-banner-opt-out__action--key-accept");
    protected By cookiesSnackBar = By.className("cookie-consent-snackbar__close");
    protected By authInputError = By.className("auth-input-error");

    public createUserPage(WebDriver driver) {
        this.driver = driver;
    }

    public void fillText(By locator, String text) {
        driver.findElement(locator).clear();
        driver.findElement(locator).sendKeys(text);
    }

    public String getTextComp(By locator){
        return driver.findElement(locator).getText();
    }

    public void clickButton(By locator) {
        driver.findElement(locator).click();
    }

    public void fillName(String text) {
        fillText(firstName,text);
    }
    public void fillLastName(String text) {
        fillText(lastName,text);
    }
    public void fillEmail(String text) {
        fillText(email,text);
    }
    public void fillPassword(String text) {
        fillText(password,text);
    }
    public void clickCheckbox(){
        clickButton(checkbox);
    }
    public void clickCreate(){
        clickButton(createButton);
    }

    public String getAuthInputText(){
        return getTextComp(authInputError);
    }

    public void closeCookiesBanner() throws InterruptedException {
        Thread.sleep(1000);
        clickButton(cookiesBanner);
        Thread.sleep(1000);
        clickButton(cookiesSnackBar);
        Thread.sleep(1000);
    }

    public boolean isCreateDisplayed(){
        return driver.findElement(createButton).isDisplayed();
    }

    public createUserConfirmationPage verificationCodePage(){
        return new createUserConfirmationPage(driver);
    }


}
```

```java
public class searchPage {

    protected WebDriver driver;

    protected By resultProductTitle = By.className("ui-search-breadcrumb__title");
    protected By resultTitle = By.xpath("(//h2[@class='ui-search-item__title");
    protected By firstElement = By.xpath("//*[@id=\"root-app\"]/div/div[1]/section/ol/li[1]/div/div/div[2]/div[1]/a/h2");
    protected By selectedItem = By.cssSelector("body > main:nth-child(3) > div:nth-child(1) > div:nth-child(3) > section:nth-child(2) > ol:nth-child(7) > li:nth-child(1) > div:nth-child(1) > div:nth-child(1)");
    public searchPage(WebDriver driver) {
        this.driver = driver;
    }

    public String getTitle(){
        return driver.getTitle();
    }

    public String getTextComp(By locator){
        return driver.findElement(locator).getText();
    }

    public String getTitleh1(){
        return getTextComp(resultProductTitle);
    }

    public String getFirstElementText(){
        return getTextComp(firstElement);
    }

    public selectedItem clickFirstElementText(){
        driver.findElement(selectedItem).click();
        return new selectedItem(driver);
    }


}
```

```java
public class selectedItem {

    protected WebDriver driver;
    protected By selectedItemTitle = By.cssSelector(".ui-pdp-title");

    public selectedItem(WebDriver driver){
        this.driver = driver;
    }

    public String getSelectedItemTitle(){
        return getTextComp(selectedItemTitle);
    }

    public String getTextComp(By locator){
        return driver.findElement(locator).getText();
    }
}
```

```java
public class sesion03_Test01 {
    WebDriver driver;

    homePage HomePage;
    searchPage SearchPage;
    helpPage HelpPage;
    termsConditionsPage TermsConditionsPage;
    createUserPage CreateUserPage;
    createUserConfirmationPage CreateUserConfirmationPage;
    selectedItem SelectedItem;

    @BeforeTest
    public static void setUpAll() {
        System.setProperty("webdriver.chrome.driver", "chromedriver.exe");
    }

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://www.mercadolibre.com.mx/");
        HomePage = new homePage(driver);
    }


    @AfterMethod
    public void close(){
        driver.close();
    }


    //1
    @Test (priority = 1)
    public void paginaInicio() {
        try{
            boolean firstResult = new WebDriverWait(driver, Duration.ofSeconds(5)).until(ExpectedConditions.titleIs("Mercado Libre México - Envíos Gratis en el día"));
        }catch (Exception e){
            System.out.println("Titulo incorrecto");
        }
    }

    //2
    @Test (priority = 2)
    public void busquedaAleatoria(){
        SearchPage = HomePage.searchProd("balon");

        String actualTitle = SearchPage.getTitle();
        String expectedTitle = "Balon | MercadoLibre \uD83D\uDCE6";

        assertEquals(expectedTitle,actualTitle);

    }

    //3
    @Test (priority = 2)
    public void verifySearchTittle(){
        SearchPage = HomePage.searchProd("Server");

        String actualTitle = SearchPage.getTitleh1();
        String expectedTitle = "Server";

        assertEquals(expectedTitle,actualTitle);
    }

    //4
    @Test (priority = 2)
    public void comprobacionContenido(){
        SearchPage = HomePage.searchProd("Nintendo");

        String firstResult = SearchPage.getFirstElementText();
        Assert.assertTrue(firstResult.contains("Nintendo"));

    }

    //5
    @Test (priority = 2)
    public  void seleccionarItem(){
        SearchPage = HomePage.searchProd("Pantalon");
        SelectedItem = SearchPage.clickFirstElementText();
        String title = SelectedItem.getSelectedItemTitle();

        Assert.assertTrue(title.contains("Pantalon") || title.contains("Jeans"));
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        System.out.println("Priority 3");

    }

    //6. Click en ayuda
    @Test (priority = 1)
    public void opcionAyuda(){

        try { HomePage.closeCookiesBanner();
        }catch(Exception e){ System.out.println("Cookies banner not displayed"); }

        HelpPage = HomePage.helpBtn();

        String actualOutput = HelpPage.helpTitle();
        String expectedOutput = "¿Con qué podemos ayudarte?";

        assertEquals(expectedOutput,actualOutput);

    }

    //7. Crear cuenta
    @Test (priority = 0)
    public void crearUsuario() throws InterruptedException {

        CreateUserPage = HomePage.createUserBtn();

        try {
            CreateUserPage.closeCookiesBanner();
        }catch(Exception e){
            System.out.println("Cookies banner not displayed");
        }

        CreateUserPage.fillName("Anita");
        CreateUserPage.fillLastName("Perez");
        CreateUserPage.fillEmail("anitaPerez@invalidmymail.com");
        CreateUserPage.fillPassword("AnitaPz!123");
        CreateUserPage.clickCheckbox();
        CreateUserPage.clickCreate();

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

    //Test 8
    @Test (priority = 1)
    public void terminosCondiciones(){

        try { HomePage.closeCookiesBanner();
        }catch(Exception e){ System.out.println("Cookies banner not displayed"); }

        TermsConditionsPage = HomePage.tycBtn();

        String actualOutput = TermsConditionsPage.tycText();
        String expectedOutput = "2- Términos y Condiciones";

        assertEquals(expectedOutput,actualOutput);
    }

    //9
    @Test (priority = 2)
    public void busquedaLibros(){

        SearchPage = HomePage.searchProd("Libro");

        String actualTitle = SearchPage.getTitleh1();
        String expectedTitle = "Libro";

        assertEquals(expectedTitle,actualTitle);

    }

    //10
    @Test (priority = 2)
    public void busquedaMousepad(){

        HomePage = new homePage(driver);
        SearchPage = HomePage.searchProd("Mousepad");

        String actualTitle = SearchPage.getTitleh1();
        String expectedTitle = "Mousepad";

        assertEquals(expectedTitle,actualTitle);

    }

    //11
    @Test(priority = 2)
    public void busquedaTeclado(){

        HomePage = new homePage(driver);
        SearchPage = HomePage.searchProd("Teclado");

        String actualTitle = SearchPage.getTitleh1();
        String expectedTitle = "Teclado";

        assertEquals(expectedTitle,actualTitle);

    }

    //12
    @Test (priority = 2)
    public void busquedaLaptop(){

        HomePage = new homePage(driver);
        SearchPage = HomePage.searchProd("Laptop");

        String actualTitle = SearchPage.getTitleh1();
        String expectedTitle = "Laptop";

        assertEquals(expectedTitle,actualTitle);
    }

}
```


