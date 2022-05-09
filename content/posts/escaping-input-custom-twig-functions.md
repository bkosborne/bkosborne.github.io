+++
author = "Brian Osborne"
title = "Escaping input for custom Twig Functions"
date = 2015-01-23
+++

I have a custom Twig function that will output a question mark icon with <a href="http://getbootstrap.com/javascript/#tooltips">tooltip</a> functionality from bootstrap. My Symfony twig extension class looks like this:

    public function getFunctions()
    {
        return array(
            new \Twig_SimpleFunction(
                'tooltip_icon',
                array($this, 'tooltipIcon'),
                array('is_safe' => array('html'))
            ),
        );
    }

    public function tooltipIcon($text, array $options = array())
    {
        $defaults = array(
            'placement' => 'top',
        );
        $options += $defaults;

        return '<span class="glyphicon glyphicon-question-sign" data-toggle="tooltip" data-placement="' . $placement . '" title="' . $text . '"></span>';
    }

Because I'm outputting HTML, I needed to tell Twig that my output is safe to render as is, and to not auto-escape it in a template. Otherwise the HTML would be escaped. However, I'm also taking user input and outputting it along with the HTML, so that needs to be escaped somehow.

For Twig *filters* you can do this by passing an additional option `'pre_escape' => 'html'` to the filter definition, telling Twig to escape input before passing it to your filter. But for Twig *functions*, the `pre_escape` option has no effect. It won't escape the main function argument or any options passed in. In fact, this is briefly mentioned in the <a href="http://twig.sensiolabs.org/doc/advanced.html#functions">Twig documentation</a>.

I was puzzled on how to manually escape input in a custom function, and couldn't find anything when searching. I turned to <a href="http://stackoverflow.com/questions/28097270">Stack Overflow</a> where I was told I can manually call `twig_escape_filter` if I saved the Twig environment when it is initialized in my class:

    protected $env;

    public function initRuntime(\Twig_Environment $env)
    {
        parent::initRuntime($env);
        $this->env = $env;
    }

    protected function escape($string)
    {
        return twig_escape_filter($this->env, $string);
    }

Now I can use the `escape` method to manually escape my functions input and options. The only thing that seems off to me is calling a global function to do the escaping, but it works.
