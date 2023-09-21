# Knowledge- <!-- omit in toc -->

Frontend: Typescript and SCSS. 

## Table of contents <!-- omit in toc -->
- [Javascript](#javascript)
  - [Value VS Reference](#value-vs-reference)
- [Angular](#angular)
  - [Standalone components](#standalone-components)
  - [Templates - We want to repeat a HTML block N times](#templates---we-want-to-repeat-a-html-block-n-times)
  - [Modules](#modules)
- [Typescript](#typescript)
  - [How to debug a component](#how-to-debug-a-component)
  - [Enums](#enums)
  - [Generic Types](#generic-types)
      - [Funcio generica:](#funcio-generica)
      - [Generic type parameter](#generic-type-parameter)
      - [Generic interface](#generic-interface)
      - [Generic classes](#generic-classes)
- [RXJS](#rxjs)
  - [Basic operators:](#basic-operators)
- [NX](#nx)
  - [How to create a component:](#how-create-component)
- [Scss](#scss)
- [Testing](#testing)
  - [Jest](#jest)
    - [Mock a service for a test component](#mock-a-service-for-a-test-component)

# NX
 ## How to create a component:
# Javascript
## Value VS Reference
If it's a primitive object => Value
```
let a = 1;
let b = a;

b = b + 2;

console.log(a); // 1
console.log(b); // 3
```
else (Objects, arrays, functions => Reference
```
let x = [1];
let y = x;

y.push(2);

console.log(x); // [1, 2]
console.log(y); // [1, 2]
```
https://dmitripavlutin.com/value-vs-reference-javascript/#:~:text=In%20JavaScript%2C%20you%20can%20pass,by%20reference%20when%20assigning%20objects.
# Angular
## Standalone components 
https://www.youtube.com/watch?v=DioXD75_t-Q
Components que no necesiten de modul per ferse servir, ells mateixos son moduls.
## Templates - We want to repeat a HTML block N times
We define the template like this. We can pass dynamic variables as context.
```
<ng-template #tableDataInput let-context="context">
    <div nxRow
     nxRowAlignItems="center"
     [formGroup]="context.element.get(context.name)"
     class="form-wrapper">
      <div nxCol="1">
        <span class="dot"
              [ngClass]="getDotColor(context.element.get(context.name))">
        </span>
      </div>
      <div nxCol="8">
        <nx-formfield nxLabel="">
          <input nxInput
                [maxlength]="context.maxlength"
                [attr.infInputNumberFormatter] = "context?.infInputNumberFormatter ? infInputNumberFormatter : null"
                [readonly] = "context?.readonly ? true : false"
                [formControlName]="context.name"/>
        </nx-formfield>
      </div>
    </div>
</ng-template>
```
Then we use the template like:
`            <ng-template [ngTemplateOutlet]="tableDataInput"
            [ngTemplateOutletContext]="{ context: {element: element, name: 'licensePlate', maxlength: 12 } }"
            ></ng-template>`
## Modules
- declarations -> Components that belong to that module.
- entryComponents -> Components that are used in that module (like components that can open one modal).

# Typescript

## How to debug a component
```
  constructor() {    
    window['c'] = this;
  }
```

## Enums
How to transform from string to enum?
https://stackoverflow.com/questions/17380845/how-do-i-convert-a-string-to-enum-in-typescript

- Method 1:
```
enum Color{
    Red, Green
}

// To String
 var green: string = Color[Color.Green];

// To Enum / number
var color : Color = Color[green];
```

- Method 2: 
```
enum Color {
  Green = "Green",
  Red = "Red"
}

const color = "Green";
const colorEnum = color as Color;
```
## Generic Types
https://www.typescriptlang.org/docs/handbook/2/generics.html#working-with-generic-type-variables
Playing arround -> https://playcode.io/931122/

#### Funcio generica:
Important: ho es quan afegim **<Pepe> angle bracket ** a la signatura
```
function identity<Type>(arg: Type): Type {
  return arg;
}
```
#### Generic type parameter	
Important: podem posar el nom que vulguem, lo important es l'ordre dels parametres
```
let myIdentity: <Jose>(arg: Jose) => Jose = identity;
console.log(myIdentity); // output: ƒ identity()
```
Podem reescriure-ho amb amb la signatura del type
` let myIdentity: { <Type>(arg: Type): Type } = identity; `
	
#### Generic interface	
```
interface GenericIdentityFn<Type> {
  (arg: Type): Type;
}

let myIdentity3: GenericIdentityFn<number> = identity;
console.log(myIdentity3('z')); // output: z
```
#### Generic classes
```
class GenericNumber<Pepe> {
  zeroValue: Pepe;
  add: (x: Pepe, y: Pepe) => Pepe;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};

console.log(myGenericNumber.add(1, 2)); // output: 3

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = 'sub_';
stringNumeric.add = function (x, y) {
  return x + y;
};

console.log(stringNumeric.add(stringNumeric.zeroValue, 'test')); // output: sub_test
```	
	

### Lambda functions
Difference between one line return or block
`(x) => x     // Equivalent to (x) => {return x}`
```	
colores.map((color) => {  
    color = 'verde'
    return color = color + '1'
}) // Returns: ['verde1', 'verde1']
```
# RXJS
RxJS is a library for reactive programming using Observables

Avoid subscribe inside a subscribe. Why it is not a good way to call subscribe inside subscribe?

Because this is not how functional programming is supposed to work. You are not thinking functionally you are thinking procedurally. This is not necessarily a problem per se, but the whole point of using RXJS (and other reactive programming extensions) is to write functional code.
I'm not going into all the details on what functional programming is but essentially the point of functional programming is to treat data as streams. Streams that are manipulated by functions and consumed by subscribers. As soon as you add a subscribe inside another subscribe your manipulating data inside a consumer (not inside a stream). So, your functional stream is now broken. This prevents other consumers from utilizing that stream further down in your code. So, you've turned your functional stream into a procedure.

![image](https://user-images.githubusercontent.com/12272206/226896344-2edb46c2-c892-4b79-94f5-12f10b10981e.png)
Source: 
*https://stackoverflow.com/questions/52317494/is-it-good-to-call-subscribe-inside-subscribe


So basics things that rxjs operator should have:
- Only have 1 subscribe
- Replace others subscribes for a RXJS operator. (“concatMap” for sequential VS “forkJoin/combineLatest” in parallel) 
- Add always untilDestroyed on the pipe. 


## Basic operators:
- filter: typical usage =>  
	`filter((result) => !isNil(result))`
- concatMap: when we want to do an operation sequentially. It must return an observable.
```
	    this.aggregationDialogService
      .openAggregationDialog()
      .pipe(
        untilDestroyed(this),
        filter((value) => !!value),
        concatMap((result: AggregationSetting) => {
          this.loaderService.show();
          const payload: AggregatedClaimBean[] = this.createPayloadAggregatedClaimBean(result);
          return this.claimHistoryService.patchClaimSummary(this.masterContractId, payload);
        }),
        concatMap(() => this.refreshTable()),
        finalize(() => this.loaderService.hide())
      )
      .subscribe(() => {
        const addSuccessText = this.translateService.instant('global.toast.addSuccess');
        this.toast.successSave('', addSuccessText);
      });
```
- tap: when we want to do some side effects. It returns a `void`. 
```
tap(([vehicleProductPackages, pricingDetails, vehicleGroups]) => {
this.vehicleProductPackages = vehicleProductPackages;
this.pricingDetailReports = pricingDetails;
this.vehicleGroups = vehicleGroups;

this.initTableColumns();
this.prepareAocData(shortRiskClassName);
this.prepareTableData();
this.updatePage();
})
```
- ForJoin: forkJoin emits only when all inner observables have completed. If you need an equivalent of forkJoin that just listens to a single emission from each source, use combineLatest + take(1)
- CombineLatest: Whenever any input Observable emits a value, it computes a formula using the latest values from all the inputs, then emits the output of that formula.
	
*Note: !!! Sometimes forkJoin is NOT working as expected and the workaround solution is using the combineLatest. Check WHY !!!
	
	
	
 

# Scss
Alinear el contingut d'un element i els fills. Dos maneres:
1. Desde el pare: 
	```
	text-align: left;
  	display: inline-block;
	```
2. Desde el fill:
	```
	margin-left: 0;
	margin-right: auto;
	```
 # NX
 ## How to create a component:
``` nx generate @nx/angular:component --name=feature/test --project=<lib name. ie client-main> ```

 
 # Testing
## Jest
### Mock a service for a test component
```
  describe('ClaimsHistoryPreviousInsurerComponent', () => {
  let component: ClaimsHistoryPreviousInsurerComponent;
  let fixture: ComponentFixture<ClaimsHistoryPreviousInsurerComponent>;
  // Service we want to mock
  let vehicleService: VehicleListService;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [FormsModule, ReactiveFormsModule, TranslateModule.forRoot(), MatDialogModule],
      declarations: [ClaimsHistoryPreviousInsurerComponent],
      // Here we add all the services that we need on the constructor
      providers: [
        { provide: VehicleListService, useValue: MockVehicleListService },
        { provide: ConfigService, useValue: MockConfigService },
        { provide: ToastService, useValue: MockToastService },
        { provide: MatDialog, useValue: MockMatDialog },
        { provide: LayoutService, useValue: MockLayoutService },
        { provide: HttpService, useValue: MockedHttpService },
        { provide: AccountService, useValue: MockAccountService },
        { provide: MAT_DIALOG_DATA, useValue: {} },
      ],
      schemas: [NO_ERRORS_SCHEMA],
    }).compileComponents();
  });

  beforeEach(() => {
    fixture = TestBed.createComponent(ClaimsHistoryPreviousInsurerComponent);
    component = fixture.componentInstance;
    component.insurerConfig = {
      multiInsurerMode: true,
      multiInsurerData: [],
    };
    // How we can acces to a private service on the constructor: component['service']
    previousInsurerService = component['previousInsurerService'];
    showAddCoverageDialogService = component['showAddCoverageDialogService'];
    vehicleService = component['vehicleListService'];
    fixture.detectChanges();
  });
	
    it('should add previous insurer', () => {
    const prevInsurer: AddPreviousInsurerDialogOutput = {
      startDate: dayjs(),
      endDate: dayjs(),
      lastClaimDate: dayjs(),
      previousInsurerId: 'V21...',
      institutionCode: INSTITUTION_CODES[0].value,
    };
    const prevInsurerRes: PreviousInsurerTO = {
      ...prevInsurer,
      lastPreexistingClaimDateTime: prevInsurer.lastClaimDate.format(DATE_TIME_FORMAT),
      startDateTime: prevInsurer.startDate.format(DATE_TIME_FORMAT),
      endDateTime: prevInsurer.endDate.format(DATE_TIME_FORMAT),
    };

    const toastSpy = jest.spyOn(component['toast'], 'successSave');
    const sortMultiInsurerDataSpy = jest.spyOn(component as any, 'sortMultiInsurerData');

    jest
      .spyOn(showAddCoverageDialogService, 'showPreviousInsurerDialog')
      .mockReturnValue(of(prevInsurer));
    jest.spyOn(previousInsurerService, 'createPreviousInsurer').mockReturnValue(of(prevInsurerRes));
    jest
      .spyOn(vehicleService, 'getVehicleClassInstitutionCode')
      .mockReturnValue(of([{ value: 'Y51', label: 'Allianz SE' }]));

    component.ngOnInit();
    component.onAddPreviousInsurer();

    expect(component.multiPrevInsurerForm.length).toBe(1);

    const formVal = component.multiPrevInsurerForm.at(0).value;
    expect(formVal).toStrictEqual({
      groupIndex: 0,
      name: INSTITUTION_CODES[0].label,
      previousInsurerId: prevInsurerRes.previousInsurerId,
      startDate: prevInsurerRes.startDateTime,
      endDate: prevInsurerRes.endDateTime,
      lastClaimDate: prevInsurerRes.lastPreexistingClaimDateTime,
    });
    expect(toastSpy).toHaveBeenCalled();
    expect(sortMultiInsurerDataSpy).toHaveBeenCalled();
    expect(component.multiPrevInsurerForm.pristine).toBeTruthy();
  });
	
});
	
```
