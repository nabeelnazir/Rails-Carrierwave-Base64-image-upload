# Rails Carrierwave Base64 image upload
This code will help you upload Base64 images with carrierwave gem without using any extra gem.


# Example with one image:
```ruby
#Sample base64 code. You can use your own.
code = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACQAAAAkCAIAAABuYg/PAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAblJREFUeNpi/P79OwO9ABMDHcHwtYyFJNXt85dfunNPT0UJyNZVUeTn5bY10CVeOyORCaR88uxpqzdhivPzcGeH+lcmRlLNsqXb92a0T8CjwNZQd9vENirE2aMXr5bu2ItfzeHzl7H6mzSfAW2ySsr7+OUrMaF0ddVcOQkx8n0GdDKRNkEUUxSMwPgnPrERdBYhy3hJsExXVZF+PqM0NULyL5FAXkKcIsuA5QXxln348oUiy7YePkm8ZQQVE7AMf74hNYIJWAYsh4i3zMZQh1KfEek5oDKCqYlw2bi8rZoYy2ZUFVChIAa6l6CTgQqIqdiIqqmjPZ0pVECaZXhSGlCKmpYBjfOxtcAZhqpKRJZqxDZ4gBUb/VpXHz9/JUOKnDaIdVI+/kISGIxPtq2gjs8IlrBE1uaELQO2ZIiJMEobPIcvXAa2Sgm2LJAL0qwQPzzpFrtlwIYi0KUkVWbIhWRVYhTWnIduGdCatvnLKE/oQCuBLWW00gBqGTCGgV6Zunoj8Q03IksDoJVZoX4QKxlfvH4D9ArQQ9S1BrMA6sxNZTSKTCMvbkgFwJqB+TWnIH06Zy/fvQcIMABF8Lc1xlKSXgAAAABJRU5ErkJggg=="
REGEXP = /\Adata:([-\w]+\/[-\w\+\.]+)?;base64,(.*)/m
new_code = code.match(REGEXP) || []
new_code = new_code[2] if new_code[2].present?
#Following patch will convert base64 code to a file and upload it using Carrierwave gem. You can loop this patch as well and you can pass dynamic original_filename and content_type attributes.
StringIO.open(Base64.decode64(new_code)) do |data|
  data.class.class_eval { attr_accessor :original_filename, :content_type }
  data.original_filename = "TEST.png"
  data.content_type = "image/png"
  profile = Profile.last
  profile.avatar = data
  profile.save!
end
```
In above code, I am calling my `profile = Profile.last` and assinging `profile.avatar = data` and then saving my profile object which will upload the image. Simple!

# Example with multiple images:
```ruby
REGEXP = /\Adata:([-\w]+\/[-\w\+\.]+)?;base64,(.*)/m
user = User.find_by_id(params[:user_id])
params[:receipt_codes].each_with_index do |code,index|
  new_code = code.match(REGEXP) || []
  new_code = new_code[2] if new_code[2].present?
  StringIO.open(Base64.decode64(new_code)) do |data|
    data.class.class_eval { attr_accessor :original_filename, :content_type }
    data.original_filename = params[:file_names][index]
    data.content_type = params[:file_types][index]
    user.receipts.create({receipt: data})
  end
end
end
```
