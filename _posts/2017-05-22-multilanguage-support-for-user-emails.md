---
layout: post
title: Multilanguage support for user email using i18n
comments: true
categories: [drupal 7]
tags: [drupal 7]
---

As of now, multilanguage support for user emails. I was in a need to create translatable strings for text entered using Example module and i used the following code to create variable

<pre>
function example_variable_info($options)
{
    $variables['field_verification_email_[mail_part]'] = array(
        'type' => 'user_mail',
        'title' => t('Account verification email', array(), $options),
        'description' => t('Customize verification e-mail messages sent to new members upon registering, when administrative approval is required.', array(), $options) . $email_token_help,
        'group' => 'user_mails',
    );

  return $variables;
}
</pre>

Now i can see theme in others tab at admin/config/regional/i18n/variable and I enabled to check box there.

Then i can translate my email template at admin/config/system/variable and **user emails**
