# Guia de Estilo de Objective-C da iOasys

Esse guia de estilo descreve as convenções de código da equipe de iOS na iOasys.

[Baseado no Guia de Estilo de Objective-C do The New York Times](https://github.com/NYTimes/objective-c-style-guide).

## Introdução

Aqui estão alguns dos documentos da Apple que influenciaram esse guia. Se algo não está mencionado aqui, provavelmente é explicado com muitos detalhes em um desses:

* [The Objective-C Programming Language](https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html#//apple_ref/doc/uid/TP30001163)
* [Cocoa Fundamentals Guide](https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html#//apple_ref/doc/uid/TP40002974-CH1-SW1)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Índice

* [Sintaxe de Ponto (dot notation)](#sintaxe-de-ponto-dot-notation)
* [Espaçamento](#espacamento)
* [Condicionais](#condicionais)
  * [Operador Ternário](#operador-ternario)
* [Tratamento de Erros](#tratamento-de-erros)
* [Métodos](#metodos)
* [Variáveis](#variaveis)
* [Nomes](#nomes)
  * [Constants](#constants)
  * [Categorias](#categorias)
* [Comentários](#comentarios)
* [Init & Dealloc](#init-and-dealloc)
* [Literais](#literais)
* [Funções CGRect](#funcoes-cgrect)
* [Constants](#constants)
* [Tipos Enumerados](#tipos-enumerados)
* [Bitmasks](#bitmasks)
* [Propriedades Privadas](#propriedades-privadas)
* [Booleanos](#booleanos)
* [Singletons](#singletons)
* [Imports](#imports)
* [Protocols](#protocols)
* [View Controllers](#view-controllers)
* [Dead Code](#dead-code)
* [Projeto do Xcode](#projeto-do-xcode)

## Sintaxe de Ponto (dot notation)

**Sempre** utilizar ponto para acessar e modificar proprieades. Sempre verifique o header da classe se não tiver certeza.

**Por exemplo:**
```objc
view.backgroundColor = [UIColor orangeColor];
textField.text = string.lowercaseString;
UIApplication.sharedApplication.delegate;
```

**Incorreto:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
[textField setText:[string lowercaseString]];
[[UIApplication sharedApplication] delegate];
```

## Espaçamento

* Idente utilizando 4 espaços. Nunca idente usando tabs. Configure essa preferencia no Xcode.
* Especificamente, as chaves de métodos devem abrir em uma nova linha. Se preferir pode ser aberto na mesma linha que a declaração, nesse caso você deve ter uma linha separando a declaração do metodo e sua implementação.

**Por exemplo:**
```objc
// preferido
- (NSInteger)randomNumber
{
    return 4;
}

// também aceito
- (NSInteger)randomNumber {
    
    return 4;
}
```

* Outras chaves (`if`/`else`/`switch`/`while` etc.) devem ser abertas na mesma linha. A chave final deve ser fechada em uma nova linha.

**Por exemplo:**
```objc
if (user.isHappy) {
    // Faz coisas
} else {
    // Faz outras coisas
}
```

* Deve haver exatamente uma linha em branco entre os métodos para melhor clareza visual e organização.
* Espaços em branco nos métodos podem separar funcionalidade (apesar de poder indicar uma oportunidade de separar um método em outros métodos menores).

## Condicionais

Condicionais devem sempre usar chaves mesmo em casos que poderiam ser escritos sem o uso de chaves (quando há apenas uma linha) para evitar [possíveis erros](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). Esses erros incluem a adição de uma segunda linha e esperar que faça parte do mesmo bloco. Outro,  [ainda mais perigoso](http://programmers.stackexchange.com/a/16530) pode acontecer quando a única linha da condicional é comentada e a próxima linha passa a fazer parte da condicional. Além disso, esse estilo é mais consistente com todas as outras condicionais e é mais faciltamente interpretado visualmente.

**Por exemplo:**
```objc
if (!error) {
    return success;
}
```

**Incorreto:**
```objc
if (!error)
    return success;
```

ou

```objc
if (!error) return success;
```

### Operador Ternário

A intenção do operador ternário, `?`, é aumentar a clareza e organização do código. O operador ternário só deve ser usado para uma condição por expressão. Ir além de uma condição prejudica a clareza do código e deve ser transformado em um `if` comum.

**Por exemplo:**
```objc
result = a > b ? x : y;
```

**Incorreto:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Tratamento de Erros

Quando métodos retornam um erro por referencia, o código deve verificar no valor retornado pelo método e não na variável de erro.

**Por exemplo:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Incorreto:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Algumas das APIs da Apple armazenam lixo no parametro de erro (se não NULL) em casos de sucesso, então verificar o erro pode causar um falso negativo (e um crash).

## Métodos

* Na assinatura de métodos, deve haver um espaço após o escopo (simbolo `-` ou `+`). Deve haver um espaço entre os parametros do método. Não deve haver um espaço antes do nome da variável.


**Por exemplo:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

**Incorreto:**
```objc
-(void) setExempleText:(NSString*)text image:(UIImage*) image;
```

* Você pode separar parametros de um método com uma nova linha, desde que estejam alinhados pelos dois pontos. Apesar de que isso não deve ser feito em métodos em que algum dos parametros é um inline block.

**Por exemplo:**
```objc
- (void)methodWithAReallyLongNameThatTakesThisParameter:(NSString *)title
                                  andThisOtherParameter:(NSUInteger)count;

[self methodWithAReallyLongNameThatTakesThisParameter:@"Hey there"
                                andThisOtherParameter:10];
```

## Variáveis

Nomes de variáveis devem ter nomes descritivos, sem medo do tamanho, com o nome da variável comunicando claramente o que _é_ a variável e informação pertinente necessário para que o programdor use o valor corretamente.

**Por exemplo:**

* `NSString *title`: É sensato assumir que "title" é uma string.
* `NSString *titleHTML`: Isso indica que o título pode conter HTML, que precisa de ser tratado antes de ser exibido. _"HTML" é necessário para que o programador use essa variável corretamente._
* `NSAttributedString *titleAttributedString`: Um título, já formatado para visualização. _`AttributedString` indica que esse valor não é um titulo qualquer e adicioná-lo pode ser uma escolha razoavel dependendo do contexto._
* `NSDate *now`: _Não é necessário explicação adicional._
* `NSDate *lastModifiedDate`: `lastModified` pode ser ambiguo; dependendo do contexto, pode ser assumido que é uma variavel de alguns tipos diferentes.
* `NSURL *URL` vs. `NSString *URLString`: Em situações que um valor pode ser representado por diferentes classes, é útil remover essa ambiguidade no nome da variável.
* `NSString *releaseDateString`: Outro exemplo onde um valor pode ser representado por outra classe e o nome auxilia em resolver essa ambiguidade.

Variáveis de apenas uma letra não são recomendadas, exceto como simples contadores em loops.

Asteriscos indicando que um tipo é um ponteiro devem estar juntos ao nome da variável. **Por exemplo**, `NSString *text` **e não** `NSString* text` ou `NSString * text`, exceto no caso de constants (`NSString * const NYTConstantString`).

Propriedades devem sempre ser usadas em vez de simples variáveis da instancia. Acesso direto à variável da instancia deve ser evitado, exceto em métodos de inicialização (`init`, `initWithCoder:`, etc…), `dealloc` e dentro de setters e getters personalizados. Para mais informações,veja [a documentação da Apple sobre usar métodos de acesso em métodos de inicialização e `dealloc`](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).


**Por exemplo:**

```objc
@interface NYTSection: NSObject

@property (strong, nonatomic) NSString *headline;

@end
```

```objc
- (void)updateUI
{
    self.textField.text = self.headline;
}
```

**Incorreto:**
```objc
- (void)updateUI
{
    _textField.text = _headline;
}
```

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

```objc
- (void)updateUI
{
    textField.text = headline;
}
```

#### Qualificadores de Propriedades

As propriedades deve ter no mínimo dois qualificadores. Qualificadores devem estar nessa ordem:
* ```weak``` / ```strong``` / ```copy``` / ```assign```
* ```nonatomic``` / ```atomic```
* ```readonly```
* ```nonnull``` / ```nullable```

Primitivos (```BOOL```, ```NSInteger```, ```CGFloat``` etc) sempre serão ```assign```, outlets ```weak``` e blocks ```copy```. Propriedades públicas sempre devem especificar sua nullability.

**Por exemplo:**
```objc
@property (weak, nonatomic) IBOutlet UITableView *tableView;
@property (strong, nonatomic) NSString *name;
@property (assign, nonatomic) NSUInteger currentIndex;
@property (copy, nonatomic) void(^callbackBlock)(id response);
```

**Incorreto:**
```objc
@property (nonatomic, strong) IBOutlet UITableView *tableView;
@property (strong) NSString *name;
@property (nonatomic) NSUinteger currentindex;
@property void(^callbackBlock)(id response);
```

#### Qualificadores de Variáveis

Quando se trata de qualificadores de variáveis [introduzidos com o ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), o qualificador (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) deve ser inserido antes do tipo da variavel, assim: `__weak NSString *text`. 

## Nomes

As convenções de nomenclatura da Apple devem ser seguidas sempre que possível, especialmente as relacionadas a [regras de gerenciamento de memória](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Nomes de variáveis e métodos longos e descritivos são bons. **Abreviações não são boas**.


**Por exemplo:**

```objc
UIButton *settingsButton;
```

**Incorreto:**

```objc
UIButton *sttgsBtn;
```

Propriedades e variáveis locais devem ser camel-case com a primeira palavra em lowercase.

Variáveis de instancia devem ser camel-case com a primeira palavra em lowercase e devem ser prefixadas com um underscore (mas você não deve criar essas variáveis). Isso é consistente com as variáveis de instancia sintetizadas automaticamente pelo LLVM. **Se o LLVM pode sintetizar a variável automaticamente, deixe-o.**

**Por exemplo:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Incorreto:**

```objc
id varnm;
```

### Constants

Constants privadas (defininas no arquivo de implementação da classe) devem usar nomes em camel-case começando com um "k":
```objc
static CGFloat const kCellHeight = 50.f;
```

Constants públicas (definidas no arquivo de header) deve inciar com a o nome da classe:
```objc
// header
FOUNDATION_EXPORT CGFloat const UserCellHeight;

// implementação, antes da @interface
CGFloat const UserCellHeight = 50.f;
```

### Categorias

Categorias são recomendadas para segmentar funcionalidade e devem ser nomeados para descrever essa funcionalidade.

**Por exemplo:**

```objc
@interface UIViewController (NYTMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Incorreto:**

```objc
@interface NYTAdvertisement (private)
@interface NSString (NYTAdditions)
```

Métodos e propriedades adicionados em categorias devem ter um prefixo do app ou organização. Isso evita sobscrever involuntariamente um método existente e reduz a chance de duas categorias de diferentes bibliotecas adicionarem um método com o mesmo nome. (O runtime do Objective-C não especifica qual método será chamado no caso anterior, o que pode levar a efeitos indesejados.)

**Por exemplo:**

```objc
@interface NSArray (NYTAccessors)
- (id)nyt_objectOrNilAtIndex:(NSUInteger)index;
@end
```

**Incorreto:**

```objc
@interface NSArray (NYTAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
```

## Comentários

Quando necessários, comentários devem ser usados para explicar **por que** esse específico pedaço de código faz algo. Todos os comentários usados devem se manter atualizados ou serem excluidos.

Comentários em bloco devem ser evitados, já que o código deve ser o mais auto-documentado possível, precisando apenas de eventuais explicações de poucas linhas. Isso não se aplica aos comentários usados para generar documentação.

## init e dealloc

Métodos `dealloc` devem estar no topo da implementação, diretamente após qualquer `@synthesize` e `@dynamic`. Métodos `init` devem ser inseridos diretamente abaixo dos métodos de `dealloc` de qualquer classe.

Métodos `init` devem ser estruturados assim:

```objc
- (instancetype)init 
{
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }

    return self;
}
```

Ao inicializar objetos, prefira `[Object new]` em vez de `[[Object alloc] init]` e outros métodos da classe quando possível.

## Literais

* Literais de `NSString`, `NSDictionary`, `NSArray`, e `NSNumber` devem sempre ser usados ao criar instancias desses objetos. Preste atenção redobrada para não passar valores `nil` nos literais de `NSArray` e `NSDictionary`, pois causará um crash.
* **Sempre** deve haver um espaço após as virgulas e dois pontos, mas **nunca** antes.
* É recomendado usar generics para especificar tipos de arrays e dicionários. Isso melhora o auto complete e clareza do código.

**Por exemplo:**

```objc
NSArray<NSString *> *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSMutableArray<NSString *> *platforms = @[@"macOS", @"iOS", @"watchOS", @"tvOS"].mutableCopy;
NSDictionary<NSString *, NSString *> *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Incorreto:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSMutableArray *platforms = [NSMutableArray arrayWithObjects:@"macOS", @"iOS", @"watchOS", @"tvOS", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## Funções `CGRect`

Ao acessar o `x`, `y`, `width` ou `altura` de um `CGRect`, o código deve usar [as funções `CGGeometry`](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) em vez de acesso direto aos membros do struct. Tirado da documentação do `CGGeometry` da Apple:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Por exemplo:**
```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Incorreto:**
```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants são preferiveis em vez de literais de string ou números, já que permitem fácil reprodução de variáveis comumente usadas e podem ser alteradas rapidamente sem a necessidade de um find e replace. Constants devem ser declaradas como constants `static`. Constants **não devem** ser declaradas com o `#define`, deixe isso exlusivamente para macros.

**Por exemplo:**
```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static CGFloat const NYTImageThumbnailHeight = 50.0;
```

**Incorreto:**
```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## Tipos Enumerados

Ao usar `enum`s, use o macro `NS_ENUM()` para facilitar a criação desse tipo.

**Examplo:**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## Bitmasks

Ao trabalhar com bitmasks, o macro `NS_OPTIONS` deve ser usado.

**Examplo:**
```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};
```

## Propriedades Privadas

Propriedades privadas devem ser declaradas em extensões da classe (categorias anonimas) no arquivo de implementação de uma classe. Na maioria dos casos, outlets devem ser privados.

**Por exemplo:**
```objc
@interface NYTAdvertisement ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```

## Booleanos

Valores não devem ser comparados diretamente a `YES`, porque `YES` é definido como `1` e um `BOOL` no Objective-C é um `CHAR` que tem 8 bits (então um valor de `11111110` irá retornar `NO` se comparado com `YES`). Comparações com `NO` ou `nil` também devem ser evitadas.

**Com ponteiro para um objeto:**

```objc
if (!someObject) {
}
```

**Incorreto:**
```objc
if (someObject == nil) {
}
```

**Para um `BOOL`:**

```objc
if (isAwesome)
if (!someNumber.boolValue)
```

**Incorreto:**

```objc
if (isAwesome == YES) // Nunca faça isso.
if (someNumber.boolValue == NO) // !someNumber.boolValue é preferido
```

`YES` e `NO` são os únicos valores aceitaveis para um `BOOL`, **jamais** use `TRUE` / `true` / `FALSE` / `false`.

Se o nome de uma propriedade `BOOL` é expressado como um adjetivo, o nome da propriedade pode omitir o prefixo `is` mas deve especificar o nome convencional para o getter.

**Por exemplo:**
```objc
@property (assign, getter=isEditable) BOOL editable;
```

_Textos e exemplos tirados do [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)._

## Singletons

Singletons devem usar um padrão thread-safe para criar sua instancia compartilhada.
```objc
+ (instancetype)sharedInstance 
{
    static id sharedInstance = nil;

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[[self class] alloc] init];
    });

    return sharedInstance;
}
```
Isso evitará [crashes frequentes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Imports

Se há mais de um import, devem ser [agrupados](http://ashfurrow.com/blog/structuring-modern-objective-c). Grupos podem ser comentados.

```objc
// Frameworks
#import <CoreLocation/CoreLocation.h>

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"
```

## Protocols

Em um [protocolo de delegate ou data source](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), o primeiro parametro de cada metodo deve ser o objeto que está enviando a mensagem.

Isso ajuda a remover a ambiguidade em casos que um objeto é o delegate para vários objetos de tipo similar e ajuda clarificar a intenção para os leitores de uma classe ao implementar esses métodos do delegate.

**Por exemplo:**
```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**Incorreto:**
```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```

Ao declarar conformidade a protocolos, prefira extensões da classe no arquivo de implementação ao não ser que a classe realmente **precise** que outros objetos saibam da sua conformidade ao protocolo.

**Por exemplo:**
```objc
@interface NYTAdvertisement () <UITableViewDataSource, UITableViewDelegate>
@end
```

## View Controllers

Métodos de lifecyle, quando usados, devem ser declarados nessa ordem:
* `viewDidLoad`
* `viewWillAppear:`
* `viewDidAppear:`
* `viewWillDisappear:`
* `viewDidDisappear:`

O storyboard ID de view controllers deve ter o mesmo nome que a classe. Um view controller de nome `HomeViewController` deve ter um storyboard ID `HomeViewController`.

## Dead Code

Métodos que não são chamados em nenhum lugar do código ou que tem uma implementação vazia são considerados dead code. Esses são os métodos padrão definidos para você pelo Xcode ao criar um novo view controller:
```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

/*
#pragma mark - Navigation

// In a storyboard-based application, you will often want to do a little preparation before navigation
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    // Get the new view controller using [segue destinationViewController].
    // Pass the selected object to the new view controller.
}
*/
```

Se não estiver fazendo nada no `didReceiveMemoryWarning` ou `viewDidLoad`, você deve excluí-los. O mesmo se aplica para o `prepareForSegue:sender:` comentado, se não estiver usando, exclua.

## Projeto do Xcode

Arquivos devem ser agrupados não só por tipo, mas também por funcionalidade.

**Por exemplo:**
```
- Models
    User.h
    User.m
    Order.h
    Order.m
- View Controllers
    InitialViewController.h
    InitialViewController.m
    - Home
        HomeViewController.h
        HomeViewController.m
    - Order
        - Cart
            CartViewController.h
            CartViewController.m
            OrderItemsTableViewController.h
            OrderItemsTableViewController.m
        - Checkout
            CheckoutViewController.h
            CheckoutViewController.m
            - Payment
                CreditCardFormViewController.h
                CreditCardFormViewController.m
                CreditCardScannerViewController.h
                CreditCardScannerViewController.m
```

Grupos não devem ser usados com o único propósito de agrupar os arquivos de header e implementação de uma única classe, apesar de que isso é aceito se uma certa funcionalidade só tem uma classe.

Warnings são do mal. Evite qualquer warning o máximo possível. Se precisar ignorar um warning especifico, use o [pragma do Clang](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
