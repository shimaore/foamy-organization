    ({expect} = require 'chai').should()

    describe 'The module', ->
      m = require '..'
      it 'shoud load', ->
        m.should.have.property 'send_mail'
        m.should.have.property 'build_transport'

      cfg =
        mailer:
          SMTP:
            host: 'smtp.example.com'
            auths:
              'noreply@example.net':
                user: 'noreply'
                pass: 'password'
          with_markdown: true
      sender = 'noreply@example.net'

      it 'should build transport', ->
        r = await m.build_transport cfg, sender
        r.should.have.property 'transporter'

      it 'should attempt to send mail', ->
        r = m.send_mail cfg,
          from: sender
          to: 'alice@example.com'
          subject: 'Testing 123'
          text: 'This is a test.'
        try
          await r
        catch error
        error.should.have.property 'message', 'getaddrinfo ENOTFOUND smtp.example.com smtp.example.com:25'
