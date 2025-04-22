# Livewire multiselect component

## Requirements
- [Tailwind](https://tailwindcss.com/)
- [Alpine JS](https://github.com/alpinejs/alpine)
- @stack('scripts') in layout blade

## Installation

You can install the package via composer:

```bash
composer require etdte/livewire-multiselect
```
## Usage

### 1) Add ```LivewireMultiselect\HasSelect``` trait to your component

```php
class Index extends Component
{
    use LivewireMultiselect\HasSelect;
```

### 2) Add variables for an available options and a selected ones to your component
```php
// for selected options - multiple
public ?array $teamFilter = null;
// for selected option - single
public int|string|null $teamFilter = null;

// available options
public ?Collection $teams = null;
```

### 3) Add the select to your page
```html
<livewire:multiselect parentId="{{ $this->getId() }}"
                      name="teamFilter"
                      label="name"
                      :selected="$teamFilter"
                      title="{{ __('Teams') }}"
                      :options="$teams"
                      :multiselect="true"
                      styles="w-full"></livewire:multiselect>
 ```

### 4) Add select listener to your component (only for Livewire v2)
If you're using Livewire v2, you need to manually add the event listener in your component:
```php
protected $listeners = ['select'];
```
For Livewire v3, this is no longer needed.

### Events
Multiselect component emits a ```multiselect.select``` event, which is caught by ```HasSelect``` trait and sets values to a proper variables in your component.<br/>
This trait also calls ```selected``` function on your component, if it exists, and passes a select name and selected items. You can define a ```selected``` method in your component to react on the changes:
<br/>**Works only if ```:simpleForm="false"```**
#### multiselect
```php
public function selected(string $name, array $value)
{
    if ($name === 'teamFilter') {
        // do something
    }
}
```
#### select
```php
public function selected(string $name, int|string|null $value)
```

### Props
| Property            | Arguments                                                                                                                                                                                           | Default     | Example                               |
|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------|---------------------------------------|
| **trackBy**         | *\<String\>* - Used to compare objects.                                                                                                                                                             | id          | ```trackBy="id"```                    |
| **label**           | *\<String\>* - Object property in option, that will be visible in the dropdown.                                                                                                                     | name        | ```label="name"```                    |  
| **name**            | *\<String\>* - Used to name inputs & identify multiple selects on a page.<br/>**Must be the same as a variable name in your component which accepts selected items**<br/>                           | *required*  | ```name="teamFilter"```               |
| **options**         | *\<Illuminate\Support\Collection\>* - Available options.                                                                                                                                            | *required*  | ```:options="$teams"```               |
| **selected**        | *\<Array,Integer,String\>* - Used to define selected options on a page loading.<br/>**Variable name should be the same as a ```name``` prop value.**                                                | ```null```  | ```:selected="$teamFilter"```         |
| **title**           | *\<String\>* - Label title for the select on a page.                                                                                                                                                | ```null```  | ```title="Teams"```                   |  
| **multiselect**     | *\<Boolean\>* - Determines if the select is multiple.                                                                                                                                               | ```false``` | ```:multiselect="true"```             |  
| **showEmptyOption** | *\<Boolean\>* - Determines if an empty option displays on the select.<br/>**Works only for single selects (```:multiselect="false"```)**                                                            | ```true```  | ```:showEmptyOption="false"```        |  
| **simpleForm**      | *\<Boolean\>* - Determines if a html input should be added to a page. <br/>Useful when a select is placed outside a livewire component, like a html form which makes a regular http request.        | ```false``` | ```:simpleForm="true"```              |  
| **parentId**        | *\<String\>* - Determines which component a select belongs to.<br/>**Highly recommended to define it when you have more then one level component depth, with the same selects name on each level.** | ```null```  | ```parentId="{{ $this->getId() }}"``` |  
| **styles**          | *\<String\>* - Classes of the root select wrapper.                                                                                                                                                  | col-span-1  | ```styles="w-full"```                 |  

---

### Additional functionality
You can refresh selected items by calling in your component 
```php
// reset component variables
$this->reset('teamFilter');
$this->anotherFilter = [2,3];
// reset options on all component selects
$this->dispatch('refresh', $this)->to('multiselect');

// you can also do something like this,
// BUT ALL NOT PASSED SELECTS, WITHIN THE COMPONENT, WILL BE RESET TO EMPTY.
$this->dispatch('refresh', ['id' => $this->getId(), 'teamFilter' => [2]])->to('multiselect');
```
You can also refresh available options 
```php
$teams = $this->teams->take(3);
// $teams - available options
// 'teamFilter' - select name
// $this->getId() - component id(parentId)
$this->dispatch('refreshOptions', $teams, 'teamFilter', $this->getId())->to('multiselect');
```

### Customizing
You can customize a select look by publishing its view
```bash
php artisan vendor:publish --tag=multiselect:views
```

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.