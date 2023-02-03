# Knowledge- <!-- omit in toc -->

Frontend: Typescript and SCSS. 

## Table of contents <!-- omit in toc -->
- [Angular](#angular)
  - [Modules](#modules)
- [Typescript](#typescript)
  - [How to debug a component](#how-to-debug-a-component)
  - [Generic Types](#generic-types)
      - [Funcio generica:](#funcio-generica)
      - [Generic type parameter](#generic-type-parameter)
      - [Generic interface](#generic-interface)
      - [Generic classes](#generic-classes)
- [Scss](#scss)

# Angular
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
