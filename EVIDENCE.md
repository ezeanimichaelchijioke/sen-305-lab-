# Evidence checklist

## Due-date query
```python
from django.utils import timezone
from requests_app.models import ServiceRequest
chosen_time = timezone.now()
due_requests = ServiceRequest.objects.filter(due_at__lt=chosen_time)
print(due_requests.query)
print(list(due_requests.values("title", "due_at", "status")))
```

## Domain validation
```python
from django.core.exceptions import ValidationError
from requests_app.models import ServiceRequest
item = ServiceRequest(status="RESOLVED", resolution_summary="")
try:
    item.full_clean()
except ValidationError as error:
    print(error.message_dict)
```
The automated tests exercise the same rule. The model `clean()` method is the enforcement point,
so it applies outside Django Admin as long as `full_clean()` is called (Django does not automatically
call `full_clean()` on every `.save()`).

## Migration review
```powershell
python manage.py sqlmigrate requests_app 0002
python manage.py migrate requests_app 0002
python manage.py showmigrations requests_app
```
