# -Component-Communication
angular2/4  Component Communication

##Angular2 父子组件间通过@Input @Output通讯

###父组件传给子组件：

####子组件设置@Input属性,父组件即可通过设置html属性给子组件传值。

```
子组件：
  @Input() title:string;
  
  @Input() set name(name:string) {
    this._name=(name&&name.trim())||'';
  }
```
set name(name:string)方法会执行,如果不需要处理setter，那么用title的形式，一行代码声明即可.

父组件调用:
```
<app-header [title]="title" name="姓名"></app-header>
```
调用方法有两种，属性名用中括号包围的title，值title是父组件中的对象名，而name没有用中括号，后面的值就是传给子组件的字符串。当然，不用中括号，也可以用{{name}}传对象的值。

如果要监听传入属性值的变化，可以在子组件实现OnChanges（@angular/core中）接口：
```
export class HeaderComponent implements OnChanges {
  ngOnChanges(changes: SimpleChanges): void {
    console.log(changes['title']);
  }
  @Input() title:string;
  _name:string = '';

  @Input() set name(name:string) {
    this._name=(name&&name.trim())||'';
  }

}
```
SimpleChanges 是一个用属性名作key的数组，通过属性名取出对象，对象里包含该属性变化前(previousValue)后(currentValue)的值。

父组件监听子组件变化
子组件通过@Output()暴露EventEmitter，父组件在声明子组件时增加EventEmitter的回调方法，子组件在需要的时候弹射事件，父组件的回调方法里就能收到。
子组件：
```
export class HeaderComponent implements OnChanges {
  ngOnChanges(changes: SimpleChanges): void {
    console.log(changes['title']);
  }
  @Input() title:string;
  _name:string = '';

  @Input() set name(name:string) {
    this._name=(name&&name.trim())||'';
  }
  //声明事件发射器
  @Output() checkEmitter=new EventEmitter<boolean>();
  //用于绑定checkbox的checked属性
  isChecked=true;

  toggle() {
    this.isChecked=!this.isChecked;
    //发射事件
    this.checkEmitter.emit(this.isChecked);
  }
}
```
子组件模板:
```
<p>
  {{title}}
</p>
<p><input type="checkbox" name="cb" [(ngModel)]="isChecked" (click)="toggle()" />Checkbox <br /></p>
```
父组件中声明:
```
<app-header [title]="title" name="{{name}}" (checkEmitter)="onCheckedChange($event)" ></app-header>
```
父组件事件回调接收:
```
export class AppComponent implements AfterViewInit{
  ngAfterViewInit() {

  }
  onCheckedChange(isChecked:boolean) {
    console.log("checkbox选中状态："+isChecked);
  }
}
```
