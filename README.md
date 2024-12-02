# django-datatables-too
Views.py

class LanguageLevelDataTableMixin(DataTableMixin, View):
    model = models.LanguageLevel
    queryset = models.LanguageLevel.objects.all()
    def _get_actions(self, obj):
        """Get action buttons w/links."""
        return f'<a href="{obj.get_update_url()}" title="Edit" class="btn btn-primary btn-xs"><i class="fa fa-pencil"></i></a> <a data-title="{obj}" title="Delete" href="{obj.get_delete_url()}" onclick="showDeletemodal({obj.id})" data-bs-toggle="modal" data-bs-target="#deleteConfirmationModal" class="btn btn-danger btn-xs btn-delete"><i class="fa fa-trash"></i></a>'
    def filter_queryset(self, qs):
        """Return the list of items for this view."""
        # If a search term, filter the query
        if self.search:
            return qs.filter(
                Q(name__icontains=self.search)
            )
        return qs
    def prepare_results(self, qs):
        data = []
        for o in qs:
            data.append({
                'name': o.name,  
                'actions': self._get_actions(o)
            })
        return data
    def get(self, request, *args, **kwargs):
        context_data = self.get_context_data(request)
        return JsonResponse(context_data)

        -----------------------------------------------------------------------------------------LIST_PAGE----------------
#LINK     \
   <link rel="stylesheet" href="https://cdn.datatables.net/1.13.4/css/jquery.dataTables.min.css">
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.datatables.net/1.13.4/js/jquery.dataTables.min.js"></script>

----  ID_PASS--
id="report-table" 
<script>
    $('#report-table').DataTable({
        columnDefs: [{
            orderable: true,
            targets: -1
        }, ],
    
        // Ajax for pagination
        processing: true,
        serverSide: true,
        ajax: {
            
            url: '{% url "adminapp:languagelevelajax" %}',  #URL THIS PALCE
            type: 'get',
        },
        columns: [
            { data: 'name', name: 'name' }, // Matches 'name' in JSON response  ADD ANY TABLE COLOUM
            { 
                data: 'actions', 
                name: 'actions', 
                orderable: false, // Disable sorting for Actions
                searchable: false // Disable search for Actions
            }
        ],
        
    });
</script>

----------------------
------------MODEL.PY---

 def get_update_url(self):
        return reverse('adminapp:careerlevelupdate', kwargs={'pk': self.pk})  #MODEL KE NICHE
    
    def get_delete_url(self):
        return reverse('adminapp:careerleveldelete', kwargs={'pk': self.pk})

----------------URL--
 path('languagelevelajax/', views.LanguageLevelDataTableMixin.as_view(), name='languagelevelajax'),
 ================================================================================================


 class LanguageLevelCreateView(CreateView):
    model = models.LanguageLevel
    form_class = forms.LanguageLevelForm
    template_name = "adminapp/languagelevel_add.html"
    success_url = reverse_lazy("adminapp:languagelevellist")
   
    def form_valid(self, form):  #THIS MATHOD
        response = super().form_valid(form)
        messages.success(self.request, "Language level added successfully!")
        return response

    def form_invalid(self, form):
        messages.error(self.request, "Error: Could not add Language level. Please check the form.")
        return super().form_invalid(form)

    
class LanguageLevelListView(ListView):
    model = models.LanguageLevel
    template_name = "adminapp/languagelevel_list.html"
    
    
class LanguageLevelUpdateView(UpdateView):
    model = models.LanguageLevel
    form_class = forms.LanguageLevelForm
    template_name = "adminapp/languagelevel_update.html"
    success_url = reverse_lazy("adminapp:languagelevellist")
    
    def form_valid(self, form): 3# THIS 
        response = super().form_valid(form)
        messages.success(self.request, "Language level updated successfully!")
        return response

    def form_invalid(self, form):
        messages.error(self.request, "Error: Could not update Language level. Please check the form.")
        return super().form_invalid(form)

    
