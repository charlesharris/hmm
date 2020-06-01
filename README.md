# hmm

```
module Siphon
  # Siphon and rebroadcast all messages in the following list
  EVENTS = [:some_event, :some_other_event]
  
  # Topic to receive rebroadcast
  TOPIC = 'lala'
  
  class << self
    # Create a class method named after each EVENTS item
    #  which will call self.rebroadcast with the event and data.
    EVENTS.each do |event|
      self.define_method(event) do |data|
        self.rebroadcast(event, data)
      end
    end

    def rebroadcast(event, event_data)
      message_body = {
        event_name: event,
        event_data: event_data
      }.to_json
      sns = ::Events::Publishers::Aws::Sns.new(TOPIC)
      Rails.logger.debug { "Rebroadcasting #{message_body.inspect}" }
      sns.publish(::Events::Event.new(event, message_body))
    end
  end
```
