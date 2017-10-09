    mailer = require 'nodemailer'
    path = require 'path'
    Promise = require 'bluebird'
    seem = require 'seem'
    smtpTransport = require 'nodemailer-smtp-transport'
    {markdown} = require 'nodemailer-markdown'

    @name = 'foamy-organization:send-mail'
    debug = (require 'tangible') @name

    build_transport = (cfg,sender) ->
      options = {}
      for own k,v of cfg.mailer.SMTP when k isnt 'auths'
        options[k] = cfg.mailer.SMTP[k]

      if sender? and cfg.mailer.SMTP.auths? and sender of cfg.mailer.SMTP.auths
        options.auth = cfg.mailer.SMTP.auths[sender]
      debug 'transport options', options

      transporter = smtpTransport options
      transport = mailer.createTransport transporter
      if cfg.mailer.with_markdown
        transport.use 'compile', markdown
          gfm: true
          tables: true
      Promise.promisifyAll transport
      transport

    send_mail = (cfg,email_options) ->
      sender = email_options.from
      build_transport(cfg,sender).sendMailAsync email_options

    module.exports = {send_mail,build_transport}
