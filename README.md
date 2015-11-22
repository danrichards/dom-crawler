DomCrawler Component *fork
==========================

DomCrawler eases DOM navigation for HTML and XML documents.

Symfony's DomCrawler 2.7.6 modified so you may swap in your own `DOMDocument`
and `DOMElement` descendant classes. If you have no need to do such a thing, 
then you should be using the regular but awesome [Symfony DomCrawler](https://github.com/symfony/dom-crawler).

The composer.json file has been modified to include Symfony's [CssSelector Component](https://github.com/symfony/css-selector)
because they're like peas and carrots :)

Install
-------

    $ composer require "dan/dom-crawler" "dev-master"


How should I apply this fork?
-----------------------------

Here is an example of how to extend `DOMDocument` and `DOMElement` and 
effectively use this fork.

Firstly, here is our awesome `DOMElement`:

```
<?php

namespace My\Awesome;

class ExtDOMDocument extends \DOMDocument
{
    public function __construct($version, $encoding)
    {
        parent::__construct($version, $encoding);
        parent::registerNodeClass('DOMElement', '\\My\\Awesome\\ExtDOMElement');
    }

    public function setRoot($name) 
    {
        return $this->appendChild(new ExtDOMElement($name));
    }
}
```

With its associative `DOMElement`:

```
<?php

namespace My\Awesome;

class ExtDOMElement extends \DOMElement
{
    public function appendElement($name) 
    {
        return $this->appendChild(new ExtDOMElement($name));
    }
}
```

Then you can use `Crawler` with your new extended classes:

```php
use Dan\Symfony\Component\DomCrawler\Crawler;

$crawler = new Crawler();
$crawler->registerDOMDocumentClass('My\\Awesome\\ExtDOMDocument');
$crawler->addContent('<html><body><p>Hello World!</p></body></html>');

$filter = $crawler->filterXPath('descendant-or-self::body/p')->first();
print get_class($filter->getNode(0)); // My\Awesome\ExtDOMElement
```

If you are also using the CssSelector component, you can use CSS Selectors
instead of XPath expressions:

```php
use Dan\Symfony\Component\DomCrawler\Crawler;

$crawler = new Crawler();
$crawler->registerDOMDocumentClass('My\\Awesome\\ExtDOMDocument');
$crawler->addContent('<html><body><p>Hello World!</p></body></html>');

print $crawler->filter('body > p')->text();

$filter = $crawler->filterXPath('descendant-or-self::body/p')->first();
print get_class($filter->getNode(0)); // My\Awesome\ExtDOMElement
```

Unit Tests
--------------------

You can run the unit tests with the following command:

    $ cd path/to/Dan/Symfony/Component/DomCrawler/
    $ composer install
    $ phpunit
    