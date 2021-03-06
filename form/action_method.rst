.. index::
    single: Forms; Changing the action and method

How to Change the Action and Method of a Form
=============================================

By default, a form will be submitted via an HTTP POST request to the same
URL under which the form was rendered. Sometimes you want to change these
parameters. You can do so in a few different ways.

If you use the :class:`Symfony\\Component\\Form\\FormBuilder` to build your
form, you can use ``setAction()`` and ``setMethod()``:

.. configuration-block::

    .. code-block:: php-symfony

        // src/Controller/DefaultController.php
        namespace App\Controller;

        use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
        use Symfony\Component\Form\Extension\Core\Type\DateType;
        use Symfony\Component\Form\Extension\Core\Type\SubmitType;
        use Symfony\Component\Form\Extension\Core\Type\TextType;

        class DefaultController extends AbstractController
        {
            public function new()
            {
                // ...

                $form = $this->createFormBuilder($task)
                    ->setAction($this->generateUrl('target_route'))
                    ->setMethod('GET')
                    ->add('task', TextType::class)
                    ->add('dueDate', DateType::class)
                    ->add('save', SubmitType::class)
                    ->getForm();

                // ...
            }
        }

    .. code-block:: php-standalone

        use Symfony\Component\Form\Forms;
        use Symfony\Component\Form\Extension\Core\Type\DateType;
        use Symfony\Component\Form\Extension\Core\Type\FormType;
        use Symfony\Component\Form\Extension\Core\Type\SubmitType;
        use Symfony\Component\Form\Extension\Core\Type\TextType;

        // ...

        $formFactoryBuilder = Forms::createFormFactoryBuilder();

        // Form factory builder configuration ...

        $formFactory = $formFactoryBuilder->getFormFactory();

        $form = $formFactory->createBuilder(FormType::class, $task)
            ->setAction('...')
            ->setMethod('GET')
            ->add('task', TextType::class)
            ->add('dueDate', DateType::class)
            ->add('save', SubmitType::class)
            ->getForm();

.. note::

    This example assumes that you've created a route called ``target_route``
    that points to the controller that processes the form.

When using a form type class, you can pass the action and method as form
options:

.. configuration-block::

    .. code-block:: php-symfony

        // src/Controller/DefaultController.php
        namespace App\Controller;

        use App\Form\TaskType;
        use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;

        class DefaultController extends AbstractController
        {
            public function new()
            {
                // ...

                $form = $this->createForm(TaskType::class, $task, [
                    'action' => $this->generateUrl('target_route'),
                    'method' => 'GET',
                ]);

                // ...
            }
        }

    .. code-block:: php-standalone

        use App\Form\TaskType;
        use Symfony\Component\Form\Forms;

        $formFactoryBuilder = Forms::createFormFactoryBuilder();

        // Form factory builder configuration ...

        $formFactory = $formFactoryBuilder->getFormFactory();

        $form = $formFactory->create(TaskType::class, $task, [
            'action' => '...',
            'method' => 'GET',
        ]);

Finally, you can override the action and method in the template by passing them
to the ``form()`` or the ``form_start()`` helper functions:

.. code-block:: twig

    {# templates/default/new.html.twig #}
    {{ form_start(form, {'action': path('target_route'), 'method': 'GET'}) }}

.. note::

    If the form's method is not GET or POST, but PUT, PATCH or DELETE, Symfony
    will insert a hidden field with the name ``_method`` that stores this method.
    The form will be submitted in a normal POST request, but Symfony's router
    is capable of detecting the ``_method`` parameter and will interpret it as
    a PUT, PATCH or DELETE request. See the :ref:`configuration-framework-http_method_override`
    option.
